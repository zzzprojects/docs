---
permaid: 1000227
Title: EF Core Oracle - Learn how to install and use this provider
MetaDescription: Unlock the power of EF Core using the Oracle Provider. Learn how to use an oracle database and which providers to install for entity framework core.
LastMod: 2023-02-22
tags: provider connection
---

# EF Core Oracle: Discover how to install and use this provider

## Introduction

The Oracle .NET team has announced they are planning to release a first-party provider for EF Core 2.0 approximately in the third quarter of 2018. For more information, see their statement of direction for [.NET Core and Entity Framework Core](https://www.oracle.com/technetwork/topics/dotnet/tech-info/odpnet-dotnet-ef-core-sod-4395108.pdf).

 - The EF team has produced a sample [EF Core provider](https://github.com/aspnet/EntityFrameworkCore/blob/dev/samples/OracleProvider/README.md) for Oracle databases. 
 - The purpose of the project is not to produce an EF Core provider owned by Microsoft, but to help us identify gaps in EF Core's relational and base functionality which we need to address in order to better support Oracle, and to jumpstart the development of other Oracle providers for EF Core by either Oracle or third parties.

## Thrid Party Provider

`Devart.Data.Oracle.EFCore` is a third party database provider and allows Entity Framework Core to be used with Oracle database. To use this database provider, the first step is to install [Devart.Data.Oracle.EFCore](https://www.nuget.org/packages/Devart.Data.Oracle.EFCore/) NuGet package. Let's consider a simple model which contains three entities.


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
        optionsBuilder.UseOracle(@"User Id=Scott;Password=tiger;Data Source=Ora;");
    } 
}
```

In EF Core, the DbContext has a virtual method called onConfiguring which will get called internally by EF Core, and it will also pass in an optionsBuilder instance, and you can use that optionsBuilder to configure options for the DbContext. 

The optionsBuilder has UseOracle method; it expects a connection string as a parameter. Once you have a model, you can use [migrations](/migrations) to create a database.

Run the following command in Package Manager Console.

`PM> Add-Migration Initial` 

This command scaffold a migration to create the initial set of tables for your model. When it is executed successfully, then run the following command.

`PM> Update-Database`

It will apply the new migration to the database. Now you can use Oracle database to insert, delete and update data.


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

#### Limitations

The dedicated team has successfully implemented most features support the Entity Framework functionality as much as possible for the Oracle Server, but there are some limitations which cannot be overcome.

 - Oracle doesn't have its equivalents for the SQL Server OUTER APPLY and CROSS APPLY constructions. 
 - User-defined types, including Oracle objects, array types and nested tables are not supported.
