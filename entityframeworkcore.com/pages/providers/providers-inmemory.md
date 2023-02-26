---
permaid: 1000224
Title: EF Core InMemory - Learn how to install and use this provider
MetaDescription: Unlock the power of EF Core using the Oracle Provider. Learn how to use an oracle database and which providers to install for entity framework core.
LastMod: 2023-02-22
tags: provider connection
---

# EF Core InMemory: Discover how to install and use this provider

## Introduction

`Microsoft.EntityFrameworkCore.InMemory` is an in-memory database provider for Entity Framework Core. It is useful when you want to test components using something that approximates connecting to the real database, without the overhead of actual database operations.

 - The advantage of in-memory provider is that you can test the code of your application against an in-memory database instead of installing and configuring the real database.
 - It is a general purpose database, designed strictly for the testing purpose of your application's code and not a relational database.

#### How to Use InMemory Provider

To use InMemory database provider, the first step is to install [Microsoft.EntityFrameworkCore.InMemory](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory/) NuGet package. Let's consider a simple model which contains three entities.


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
    public MyContext(DbContextOptions<MyContext> options)
        : base(options)
    { }
    public DbSet<Customer> Customers { get; set; }
    public DbSet<Invoice> Invoices { get; set; }
    public DbSet<InvoiceItem> InvoiceItems { get; set; }
}
```

To use the InMemory database, create an instance of `DbContextOptions<MyContext>` and pass this instance to the constructor of the `MyContext` class when instantiating it.


```csharp
var options = new DbContextOptionsBuilder<MyContext>()
   .UseInMemoryDatabase(databaseName: "Test")
   .Options;

using (var context = new MyContext(options))
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

InMemory is a general purpose database provider for testing and is not designed for a relational database.

 - InMemory will allow you to save data that would violate referential integrity constraints in a relational database.
 - If you use DefaultValueSql(string) for a property in your model, this is a relational database API and will not affect when running against InMemory.
