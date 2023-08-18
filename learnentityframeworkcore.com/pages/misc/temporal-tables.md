---
title: Temporal Tables in Entity Framework Core
description: Temporal Tables automatically keep track of all the data ever stored in a table, even after that data has been updated or deleted.
canonical: /misc/temporal-tables
status: Published
lastmod: 2023-02-16
---

# EF Core Temporal Tables

## What is a Temporal Table?

Temporal Tables is a feature in Microsoft SQL Server that allows you to store and manage data history, enabling you to track changes and retrieve previous versions of data. A temporal table is a regular table that has a built-in system-versioning mechanism, which automatically tracks changes to the data and stores a history of changes over time.

 - Temporal tables work by adding two additional columns to the table: a start time column and an end time column. 
 - The start time column stores the time that a row was created or updated, and the end time column stores the time that the row was modified or deleted. 
 - As changes are made to the data, new versions of the rows are created and stored in the table, and the previous versions of the rows are marked with an end time.

By using temporal tables, you can easily track changes to the data over time, including who made the change and when it was made. You can also query the table to retrieve the state of the data at any point in time, making it easy to analyze trends and patterns in the data.

## Temporal Tables

EF Core provides support for working with SQL Server Temporal Tables by exposing the historical data stored in these tables as entity objects in your .NET applications.

 - To use temporal tables with EF Core, you need to define your temporal table as a normal entity class in your .NET application, and then configure that entity type by mapping it to temporal tables in the `OnModelCreating` using the `IsTemporal` method. 
 - Once you have configured the temporal, you can perform CRUD operations on the table just like you would with any other entity. 
 - EF Core will automatically handle the underlying logic for tracking changes to the data and storing historical data in the temporal table.

To use temporal tables in EF Core, you need to add the `IsTemporal` method in the `OnModelCreating` method of your context class. The `IsTemporal` method can be called on an entity type to configure it as a system-versioned temporal table.

Here's an example of how you can use the `IsTemporal` method in the `OnModelCreating` method of your context class:

```csharp
public class TemporalContext : DbContext
{
    public DbSet<Customer> Customers { get; set; }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
        optionsBuilder.UseSqlServer(@"Data Source=(localdb)\ProjectsV13;Initial Catalog=TemporalDb;");
    }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Customer>()
            .ToTable("Customers", c => c.IsTemporal());
    }
}

public class Customer
{
    public int Id { get; set; }
    public string? Name { get; set; }
}
```

In this example, the `TemporalContext` class is configured as a system-versioned temporal table in the `OnModelCreating` method. The `ToTable` method is used to specify the name of the table in the database. The IsTemporal method is called to configure the entity as a system-versioned temporal table.

```csharp
CREATE TABLE [dbo].[Customers] (
    [Id]          INT                                                IDENTITY (1, 1) NOT NULL,
    [Name]        NVARCHAR (MAX)                                     NULL,
    [PeriodEnd]   DATETIME2 (7) GENERATED ALWAYS AS ROW END HIDDEN   NOT NULL,
    [PeriodStart] DATETIME2 (7) GENERATED ALWAYS AS ROW START HIDDEN NOT NULL,
    CONSTRAINT [PK_Customers] PRIMARY KEY CLUSTERED ([Id] ASC),
    PERIOD FOR SYSTEM_TIME ([PeriodStart], [PeriodEnd])
)
WITH (SYSTEM_VERSIONING = ON (HISTORY_TABLE=[dbo].[CustomersHistory], DATA_CONSISTENCY_CHECK=ON));
```

As you can see the SQL Server creates two hidden `DATETIME2` columns `PeriodEnd` and `PeriodStart`. 

 - These columns represent the time range during which the data in the row existed. 
 - These columns are mapped to shadow properties in the EF Core model, allowing them to be used in queries.

Most of the time, temporal tables are used just like any other table. Let's use the temporal table by adding some data.

```csharp
using (var context = new TemporalContext())
{
    context.AddRange(
        new Customer { Name = "John Doe" },
        new Customer { Name = "Jane Pie" },
        new Customer { Name = "Rainbow Dash"},
        new Customer { Name = "Mark Upston"});
    context.SaveChanges();
}
```

In this example, new instances of the `Customer` class are created and added to the database using the `AddRange` method. The changes are then saved to the database using the `SaveChanges` method in the context.

## Querying Historical Data

EF Core supports queries that include historical data through several new query operators:

 - **TemporalAsOf:** Returns rows that were active (current) at the given UTC time. This is a single row from the current table or history table for a given primary key.
 - **TemporalAll:** Returns all rows in the historical data. This is typically many rows from the history table and/or the current table for a given primary key.
 - **TemporalFromTo:** Returns all rows that were active between two given UTC times. This may be many rows from the history table and/or the current table for a given primary key.
 - **TemporalBetween:** The same as `TemporalFromTo`, except that rows are included that became active on the upper boundary.
 - **TemporalContainedIn:** Returns all rows that started being active and ended being active between two given UTC times. This may be many rows from the history table and/or the current table for a given primary key.

Here's an example of how you can retrieve historical data from a temporal table:

```csharp
using (var context = new TemporalContext())
{
    var history = context
        .Customers
        .TemporalAll()
        .Where(c => c.Name == "Rainbow Dash")
        .OrderBy(c => EF.Property<DateTime>(c, "PeriodStart"))
        .Select(
            c => new
            {
                Customer = c,
                ValidFrom = EF.Property<DateTime>(c, "PeriodStart"),
                ValidTo = EF.Property<DateTime>(c, "PeriodEnd")
            })
        .ToList();

    foreach (var pointInTime in history)
    {
        Console.WriteLine(
            $"  Customer {pointInTime.Customer.Name} from {pointInTime.ValidFrom} to {pointInTime.ValidTo}");
    }
}
```

## **Note** 

The `IsTemporal` method requires a database provider that supports system-versioned temporal tables. For example, SQL Server has built-in support for system-versioned temporal tables.
