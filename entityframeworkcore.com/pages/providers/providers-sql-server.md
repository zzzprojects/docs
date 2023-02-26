---
permaid: 1000229
Title: EF Core SQL Server - Learn how to install and use this provider
MetaDescription: Unlock the power of EF Core using the SQL Server Provider. Learn how to use the microsoft sql server database and which providers to install for entity framework core.
LastMod: 2023-02-22
tags: provider connection
---

# EF Core SQL Server: Discover how to install and use this provider

## Introduction

`Microsoft.EntityFrameworkCore.SqlServer` database provider allows Entity Framework Core to be used with Microsoft SQL Server (including SQL Azure). The provider is maintained as part of the [Entity Framework Core](https://github.com/aspnet/EntityFrameworkCore) Project.

#### How to Use SQL Server Provider

To use SQL Server database provider, the first step is to install [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/) NuGet package. Let's consider a simple model which contains three entities.


```csharp
public class Customer
{
    public int CustomerId { get; set; }
    public string FirstName { get; set; }
    public string LastName { get; set; }
    public string Address { get; set; }
    public virtual List<Invoice> Invoices { get; set; }
}

public class Invoice
{
    public int Id { get; set; }
    public DateTime Date { get; set; }

    public int CustomerId { get; set; }

    [ForeignKey("CustomerId")]
    public virtual Customer Customer { get; set; }
    public virtual List<InvoiceItem> Items { get; set; }
}

public class InvoiceItem
{
    public int InvoiceItemId { get; set; }
    public int InvoiceId { get; set; }
    public string Code { get; set; }

    [ForeignKey("InvoiceId")]
    public virtual Invoice Invoice { get; set; }
}
```

The next step is to create a custom `DbContext` class.


```csharp
public class MyContext : DbContext
{
    public DbSet<Customer> Customers { get; set; }
    public DbSet<Invoice> Invoices { get; set; }
    public DbSet<InvoiceItem> InvoiceItems { get; set; }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
        optionsBuilder
            .UseSqlServer(@"Data Source=(localdb)\ProjectsV13;Initial Catalog=CustomerDB;");
    }
}
```

In EF Core, the DbContext has a virtual method called onConfiguring which will get called internally by EF Core, and it will also pass in an optionsBuilder instance, and you can use that optionsBuilder to configure options for the DbContext. 

The optionsBuilder has UseSqlServer method; it expects a connection string as a parameter. Once you have a model, you can use [migrations](/migrations) to create a database.

Run the following command in Package Manager Console.

`PM> Add-Migration Initial` 

This command scaffold a migration to create the initial set of tables for your model. When it is executed successfully, then run the following command.

`PM> Update-Database`

It will apply the new migration to the database. Now you can use SQL Server database to insert, delete and update data.


```csharp
using (var context = new MyContext())
{
    var customer = new Customer
    {
        FirstName = "Elizabeth",
        LastName = "Lincoln",
        Address = "23 Tsawassen Blvd."
    };

    context.Customers.Add(customer);
    context.SaveChanges();
}
```

## Memory-Optimized Tables

Memory-Optimized Tables are a feature of SQL Server where the entire table resides in memory. 

 - A second copy of the table data is maintained on disk, but only for durability purposes. 
 - Data in memory-optimized tables is only read from disk during database recovery. 

You can specify that the table which mapped to an entity is memory-optimized. 


```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Customer>()
        .ForSqlServerIsMemoryOptimized();
}
```
