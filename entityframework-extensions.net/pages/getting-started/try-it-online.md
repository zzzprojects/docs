---
Title: Try Entity Framework Extensions Online with .NET Fiddle
MetaDescription: Learn how to try Entity Framework Extensions online with .NET Fiddle, configure EF Core, create tables, and run bulk operations without a local database.
LastMod: 2026-07-13
---

# Try Entity Framework Extensions Online

You can try Entity Framework Extensions directly in your browser with [.NET Fiddle](https://dotnetfiddle.net/).

While learning Entity Framework Extensions, there is no need to create a project, manually install the NuGet packages, or configure a local database.

Simply add the following directives at the top of your code:

```csharp
// @nuget: Microsoft.EntityFrameworkCore.SqlServer
// @nuget: Z.EntityFramework.Extensions.EFCore
```

Then add the following `using` directives:

```csharp
using Microsoft.EntityFrameworkCore;
using Z.EntityFramework.Extensions;
```

Entity Framework Extensions requires a configured `DbContext` to execute an operation. One simple way to connect to the SQL Server database provided by .NET Fiddle is to override the `OnConfiguring` method:

```csharp
public class EntityContext : DbContext
{
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
        optionsBuilder.UseSqlServer(
            FiddleHelper.GetConnectionStringSqlServer());
    }
}
```

You can then use the `EnsureCreated` method to create the required tables from your EF Core model:

```csharp
using var context = new EntityContext();

context.Database.EnsureCreated();
```

The tables are automatically created based on the entities and configuration in your EF Core model, so you do not need to write a `CREATE TABLE` statement.

.NET Fiddle automatically downloads the required NuGet packages before compiling your code.

Everything runs directly in your browser, so you can start experimenting in just a few seconds.

In many of our online examples, we use the .NET Fiddle **Explorer** to store the `DbContext`, entity classes, and other supporting code in separate files.

This keeps the main file focused on the Entity Framework Extensions feature being demonstrated.

## Try a Basic Bulk Insert

The following example shows how to create the required table and insert multiple customers with the `BulkInsert` method:

```csharp
// @nuget: Microsoft.EntityFrameworkCore.SqlServer
// @nuget: Z.EntityFramework.Extensions.EFCore

using System;
using System.Collections.Generic;
using System.Linq;
using Microsoft.EntityFrameworkCore;
using Z.EntityFramework.Extensions;

public class Program
{
    public static void Main()
    {
        using var context = new EntityContext();

        context.Database.EnsureCreated();

        var customers = new List<Customer>
        {
            new Customer { Name = "Alice", Email = "alice@example.com" },
            new Customer { Name = "Bob", Email = "bob@example.com" }
        };

        // Insert all customers
        context.BulkInsert(customers);

        Console.WriteLine($"{customers.Count} customers inserted.");

        // Query and display all customers
        FiddleHelper.WriteTable(context.Customers.ToList());
    }
}

public class EntityContext : DbContext
{
    public DbSet<Customer> Customers { get; set; }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
        optionsBuilder.UseSqlServer(
            FiddleHelper.GetConnectionStringSqlServer());
    }
}

public class Customer
{
    public int CustomerID { get; set; }

    public string Name { get; set; }

    public string Email { get; set; }
}
```

[Online Example](https://dotnetfiddle.net/FSFspV)

The `CustomerID` property is automatically recognized by EF Core as the primary key and configured as an identity column by convention.

You can click the **Online Example** link to open and run the example directly in .NET Fiddle.

Alternatively, you can copy the code into a new .NET Fiddle.

You can then modify the customers, their values, the EF Core model, or the bulk operation to experiment with your own scenarios.

## Try Any Documentation Example

You can use the same approach with almost any example from this documentation:

* Open .NET Fiddle.
* Add the required NuGet directives at the top of the code.
* Copy the example you want to try.
* Add any required `using` directives.
* Configure the `DbContext` with the SQL Server connection provided by .NET Fiddle.
* Add the entities and model configuration required by the example.
* Create the required tables with the `EnsureCreated` method.
* Click **Run**.

```csharp
// @nuget: Microsoft.EntityFrameworkCore.SqlServer
// @nuget: Z.EntityFramework.Extensions.EFCore

using Microsoft.EntityFrameworkCore;
using Z.EntityFramework.Extensions;

public class EntityContext : DbContext
{
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
        optionsBuilder.UseSqlServer(
            FiddleHelper.GetConnectionStringSqlServer());
    }
}
```

Add the entities, `DbSet` properties, relationships, and model configuration required by the example you want to try.

Some examples may require additional NuGet packages, entities, relationships, model configuration, or setup code. The **Online Example** links in our documentation already include everything required to run them.

This is often the fastest way to:

* Test a bulk operation.
* Explore a feature.
* Try different options.
* Reproduce an issue.
* Compare different approaches.
* Share an example with someone else.

## More Online Examples

If you want to explore more features, you can browse our collection of online examples.

Every example is ready to run and edit in .NET Fiddle, making it easy to learn new features, experiment with different options, or adapt the code to your own scenarios.

[Browse All Online Examples](/online-examples)

## Summary

In this article, you learned how to use .NET Fiddle to try Entity Framework Extensions without creating a project, manually installing the required NuGet packages, or configuring a local database.

Continue with the following articles to learn more about Entity Framework Extensions:

* [Bulk Insert](/bulk-insert)
* [Bulk Update](/bulk-update)
* [Bulk Delete](/bulk-delete)
* [Bulk Merge](/bulk-merge)
* [Bulk SaveChanges](/bulk-savechanges)
