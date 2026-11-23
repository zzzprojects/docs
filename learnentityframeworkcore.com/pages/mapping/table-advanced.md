---
title: Table (Advanced) in EF Core
description: Learn how to configure Table Splitting, Entity Splitting, and SQL Server table-valued functions in EF Core using Fluent API.
canonical: /mapping/table-advanced
status: Published
lastmod: 2026-11-23
---

# Table (Advanced) in EF Core

EF Core provides advanced mapping options for sharing tables, distributing entity properties across tables, and querying database functions using Fluent API.

## Table Splitting

**Table Splitting** maps multiple entities to the same database row using a shared primary key and a one-to-one relationship. For more about configuring this relationship, see [One-To-One Relationship Configuration](/modeling/relationships-one-to-one).

In this example, `Order` and `DetailedOrder` share the `Orders` table:

```csharp
public class Order
{
    public int Id { get; set; }
    public string Status { get; set; } = string.Empty;
    public DetailedOrder DetailedOrder { get; set; } = null!;
}

public class DetailedOrder
{
    public int Id { get; set; }
    public string BillingAddress { get; set; } = string.Empty;
    public string ShippingAddress { get; set; } = string.Empty;
}

public class ApplicationContext : DbContext
{
    public DbSet<Order> Orders => Set<Order>();
    public DbSet<DetailedOrder> DetailedOrders => Set<DetailedOrder>();

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Order>(entity =>
        {
            entity.ToTable("Orders");

            entity.HasOne(o => o.DetailedOrder)
                .WithOne()
                .HasForeignKey<DetailedOrder>(d => d.Id);

            entity.Navigation(o => o.DetailedOrder).IsRequired();
        });

        modelBuilder.Entity<DetailedOrder>()
            .ToTable("Orders");
    }
}
```

In this example:

- `ToTable("Orders")` maps both entities to the same database table.
- `HasOne()`, `WithOne()`, and `HasForeignKey()` configure a one-to-one relationship in which `DetailedOrder.Id` is both its primary key and a foreign key to `Order.Id`.
- `IsRequired()` configures `DetailedOrder` as a required dependent in the EF Core model.

EF Core maps both entities to a single `Orders` table containing the `Id`, `Status`, `BillingAddress`, and `ShippingAddress` columns. Both entities share the same primary key value and represent the same database row.

Table Splitting is useful when you want to organize an entity's basic information and additional details into separate C# classes without creating separate database tables.

> **Note:** This example uses a required dependent entity. Optional dependents, concurrency tokens, and inheritance introduce additional considerations. See Microsoft's [Advanced Table Mapping documentation](https://learn.microsoft.com/en-us/ef/core/modeling/table-splitting) for these scenarios.

## Entity Splitting

**Entity Splitting** works in the opposite direction: it maps a single entity to multiple database tables using `SplitToTable()`. The application works with one entity even though its properties are stored in different tables.

The following example maps `Customer` to two tables, `Customers` and `CustomerDetails`:

```csharp
public class Customer
{
    public int Id { get; set; }
    public string Name { get; set; } = string.Empty;
    public string PhoneNumber { get; set; } = string.Empty;
    public string City { get; set; } = string.Empty;
}

public class ApplicationContext : DbContext
{
    public DbSet<Customer> Customers => Set<Customer>();

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Customer>(entity =>
        {
            entity.ToTable("Customers")
                .SplitToTable("CustomerDetails", tableBuilder =>
                {
                    tableBuilder.Property(c => c.Id)
                        .HasColumnName("CustomerId");
                    tableBuilder.Property(c => c.PhoneNumber);
                    tableBuilder.Property(c => c.City);
                });
        });
    }
}
```

In this example:

- `ToTable("Customers")` maps `Id` and `Name` to the main `Customers` table.
- `SplitToTable("CustomerDetails")` maps `PhoneNumber` and `City` to a separate table.
- `HasColumnName("CustomerId")` specifies the key column name in `CustomerDetails`.

The `Customers` table contains the customer's ID and name, while `CustomerDetails` contains the corresponding ID, phone number, and city.

EF Core uses the shared key to associate the rows. When querying `Customer`, it retrieves the properties from both tables and materializes a single entity. Similarly, updates to mapped properties are persisted to their corresponding tables.

Unlike Table Splitting, which uses multiple entities for one table, Entity Splitting maintains one entity across multiple tables.

> **Note:** Each row in the main table must have a corresponding row in every split table. Optional table fragments are not supported, and Entity Splitting cannot be used with entity types in inheritance hierarchies. See Microsoft's [Entity Splitting documentation](https://learn.microsoft.com/en-us/ef/core/modeling/table-splitting#entity-splitting).

## Table-Valued Function

EF Core also allows an entity to be mapped to a **table-valued function (TVF)** using `ToFunction()`. Instead of reading from a regular table, EF Core queries the rows returned by a database function.

The following example maps `OrderSummary` to a SQL Server function named `GetOrderSummary`:

```csharp
public class OrderSummary
{
    public int Id { get; set; }
    public string Status { get; set; } = string.Empty;
}

// In OnModelCreating
modelBuilder.Entity<OrderSummary>()
    .HasNoKey()
    .ToFunction("GetOrderSummary");
```

The SQL Server function returns the order ID and status from the `Orders` table used in the Table Splitting example:

```sql
CREATE FUNCTION dbo.GetOrderSummary()
RETURNS TABLE
AS
RETURN
(
    SELECT Id, Status
    FROM dbo.Orders
);
```

After creating the function in the database, you can query its results using LINQ:

```csharp
var orders = await context.Set<OrderSummary>()
    .Where(o => o.Status == "Pending")
    .ToListAsync();
```

In this example:

- `ToFunction("GetOrderSummary")` maps `OrderSummary` to the database function rather than a regular table.
- `HasNoKey()` configures the result as a keyless, read-only entity type.
- `Where()` filters the returned orders, with EF Core translating the LINQ expression into SQL.

The function returns the `Id` and `Status` columns, matching the properties of `OrderSummary`. EF Core queries the function and materializes its results without tracking them for updates.

Unlike Table Splitting and Entity Splitting, this configuration does not change how the `Orders` table is mapped or stored. It provides a way to query data returned by a database function.

> **Note:** The function must already exist in the database. Direct entity mapping with `ToFunction()` requires a parameterless TVF, and `HasNoKey()` is a choice for this read-only example, not a requirement of every TVF mapping. For parameterized functions, see Microsoft's [User-Defined Function Mapping documentation](https://learn.microsoft.com/en-us/ef/core/querying/user-defined-function-mapping#mapping-a-queryable-function-to-a-table-valued-function).

## External Resources - Advanced Table Mapping

The following videos provide practical demonstrations of advanced table mapping in EF Core. They show how multiple entities can share a database table and how SQL Server table-valued functions can be created and queried through EF Core.

### Video 1 - 23- Table Splitting - Una tabla, varias clases | Entity Framework Core 2.0

<iframe width="560" height="315" src="https://www.youtube.com/embed/rIo5vtMu-tQ" title="23- Table Splitting - Una tabla, varias clases | Entity Framework Core 2.0" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

In this Spanish tutorial, Felipe Gavilán demonstrates how two entities can share a single database table using a shared primary key and a one-to-one relationship.

**Key timestamps:**

- [**01:29**](https://www.youtube.com/watch?v=rIo5vtMu-tQ&t=89s) — Creates the detail entity with a shared primary key and additional properties.
- [**03:13**](https://www.youtube.com/watch?v=rIo5vtMu-tQ&t=193s) — Configures the one-to-one relationship using Fluent API.
- [**03:47**](https://www.youtube.com/watch?v=rIo5vtMu-tQ&t=227s) — Maps the detail entity to the existing table using `ToTable()`.
- [**08:01**](https://www.youtube.com/watch?v=rIo5vtMu-tQ&t=481s) — Uses `Include()` to retrieve the related details.

Although the demonstration uses EF Core 2.0, it provides a practical introduction to Table Splitting, from configuring the relationship to retrieving the related entity.

### Video 2 - Entity Framework Core #8 - Table Splitting

<iframe width="560" height="315" src="https://www.youtube.com/embed/laQDOpV7mAc" title="Entity Framework Core #8 - Table Splitting" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

André Secco demonstrates Table Splitting in Portuguese using `Livro` and `LivroDetalhes`, focusing on how EF Core maps two related entities to a single database table.

**Key timestamps:**

- [**01:50**](https://www.youtube.com/watch?v=laQDOpV7mAc&t=110s) — Introduces the `Livro` and `LivroDetalhes` classes and their properties.
- [**03:35**](https://www.youtube.com/watch?v=laQDOpV7mAc&t=215s) — Configures the one-to-one relationship between the entities.
- [**04:20**](https://www.youtube.com/watch?v=laQDOpV7mAc&t=260s) — Maps both entities to the `Livros` table.
- [**09:44**](https://www.youtube.com/watch?v=laQDOpV7mAc&t=584s) — Inspects the generated table and its columns in SQL Server.

This EF Core 2.1 demonstration complements the previous video by showing how EF Core reconstructs the related objects and how their properties are stored in one physical table.

### Video 3 - Entity Framework Core with Table-Valued Functions in C#

<iframe width="560" height="315" src="https://www.youtube.com/embed/27jE_D2UrsU" title="Entity Framework Core with Table-Valued Functions in C#" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

Tural Suleymani demonstrates how to create a parameterized table-valued function in SQL Server and integrate it with EF Core, including function creation, migrations, and query mapping.

**Key timestamps:**

- [**03:58**](https://www.youtube.com/watch?v=27jE_D2UrsU&t=238s) — Creates a SQL Server table-valued function with a parameter.
- [**12:50**](https://www.youtube.com/watch?v=27jE_D2UrsU&t=770s) — Adds the function's SQL script to an EF Core migration.
- [**14:44**](https://www.youtube.com/watch?v=27jE_D2UrsU&t=884s) — Defines an `IQueryable` method using `FromExpression()`.
- [**15:50**](https://www.youtube.com/watch?v=27jE_D2UrsU&t=950s) — Maps the method to the database function using `HasDbFunction()`.

Unlike the parameterless function mapped with `ToFunction()` in our example, this video demonstrates a parameterized TVF using `HasDbFunction()`. It offers a complementary approach for readers who need to pass arguments to database functions.

## Summary

EF Core provides three advanced mapping options for different scenarios:

- **Table Splitting** maps multiple entities to the same table and row using a shared primary key.
- **Entity Splitting** maps a single entity across multiple tables using `SplitToTable()`.
- **Table-Valued Function** maps an entity to a database function using `ToFunction()`, allowing its results to be queried with LINQ.

## Related Articles

- [EF Core One To One Relationship](/modeling/relationships-one-to-one)
- [Table](/mapping/table)
- [Column](/mapping/column#column-name)

## FAQ

### Does Table Splitting require navigation properties on both entities?

No. A one-to-one relationship can be configured with a navigation property on only one side. For example, `Order` can reference `DetailedOrder` without requiring a navigation back to `Order`.

Both entity types must still be mapped to the same table and share a primary key through their relationship.

### Does Entity Splitting require a separate DbSet for each table?

No. Entity Splitting uses a single entity type, even when its properties are stored in multiple tables.

In our example, `DbSet<Customer>` is enough to query `Customer`. EF Core uses the mapping configured with `SplitToTable()` to retrieve its properties from `Customers` and `CustomerDetails`.

### Does Entity Splitting affect query performance?

It can. Because an entity's properties are stored in multiple tables, EF Core generally needs to join those tables when retrieving the entity.

The performance impact depends on the generated SQL, data volume, and database indexes. Measure query performance before choosing Entity Splitting for performance-sensitive scenarios.

### Does ToFunction() create the SQL function in the database?

No. `ToFunction()` configures EF Core to query an existing table-valued function; it does not create the SQL function itself.

You can create the function directly in the database or include its SQL definition in an [EF Core migration](/migrations) using `migrationBuilder.Sql()`. The function must exist before the mapped entity can be queried successfully.
