---
title: EF Core Bulk Insert with Entity Framework Extensions
description: Learn how to use the Bulk Insert extension method from Entity Framework Extensions to insert entities in the database using the fewest possible round-trips. This method, created by ZZZ Projects, is especially useful for improving database performance or when you need to insert data using a custom key or take advantage of the many available options.
canonical: /bulk-extensions/bulk-insert
status: Published
lastmod: 2025-07-23
---

# EF Core Bulk Insert with Entity Framework Extensions

The `BulkInsert` extension method is part of the [Entity Framework Extensions](https://entityframework-extensions.net/) library.  
It lets you insert a large number of entities into your database in a single operation—dramatically reducing the number of round-trips and improving performance.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

var customers = new List<Customer>
{
    new Customer { Name = "ZZZ Projects", City = "New York" },
    new Customer { Name = "Jonathan Magnan", City = "Montreal" }
    // Add more customers as needed
};

using (var context = new YourDbContext())
{
    // Easy to use
    context.BulkInsert(customers);
    
    // Easy to customize
    context.BulkInsert(customers, options => options.IncludeGraph = true);
}

using (var context = new YourDbContext())
{
    // Supports async
    await context.BulkInsertAsync(customers);
}
````

In this example, we create a list of `Customer` objects and insert them all at once using the `BulkInsert` method.
The operation is simple, efficient, and supports many customization options.

The `BulkInsert` method works directly with your `DbContext` or `DbSet`, and under the hood, it uses the best available bulk insert mechanism for your database provider—like `SqlBulkCopy` for SQL Server.

You can also fine-tune the behavior with options like:

* ✅ Setting a batch size to control how many rows are inserted per round-trip
* ✅ Mapping properties to columns
* ✅ Customizing timeout, transactions, and more

The method is perfect when working with large datasets or during data migration.

---

## 🚀 Why Use the Bulk Insert Method from Entity Framework Extensions?

The `BulkInsert` method is the best choice when you need to perform high-performance insert operations.
**Inserting with Entity Framework Extensions is up to 14x faster**, reducing execution time by **93%**.
👉 [View Benchmark](https://dotnetfiddle.net/cFWgKV)

It also comes with several other benefits:

* ✅ **Blazing fast performance** — Insert millions of rows in seconds. [See Performance Comparison](https://entityframework-extensions.net/bulk-insert#performance-comparison)
* ✅ **Memory-efficient** — Works well even with very large datasets
* ✅ **Fully customizable** — With hundreds of options to fit your needs. [Explore Options](https://entityframework-extensions.net/bulk-insert#bulk-insert-options)

---

## 📦 How to Install Entity Framework Extensions

To use the `BulkInsert` method, install the [Z.EntityFramework.Extensions.EFCore NuGet package](https://www.nuget.org/packages/Z.EntityFramework.Extensions.EFCore/):

```powershell
PM> NuGet\Install-Package Z.EntityFramework.Extensions.EFCore
```

```bash
> dotnet add package Z.EntityFramework.Extensions.EFCore
```

Make sure the **first digit** of the version matches your EF Core version.
For example, if you're using `EF Core 9`, use version `v9.x.y.z` of the package.

Learn more about [EF Core pinned versioning](https://entityframework-extensions.net/efcore-pinned-versioning).

Need another version like EF6? Visit the official downloads page:
👉 [https://entityframework-extensions.net/download](https://entityframework-extensions.net/download)

---

## 🔗 External Links

Here are some useful links to help you get started with **Entity Framework Extensions** and its **Bulk Extension methods**, like `BulkInsert`:

* 🔗 [Bulk Insert Documentation](https://entityframework-extensions.net/bulk-insert)
* 🔗 [Entity Framework Extensions Overview](https://entityframework-extensions.net/)
* 🔗 [NuGet Package](https://www.nuget.org/packages/Z.EntityFramework.Extensions.EFCore)
* 🔗 [ZZZ Projects (Official Site)](https://zzzprojects.com/)
