---
title: EF Core Bulk Insert with Entity Framework Extensions
description: Learn how to use the BulkInsert extension method from Entity Framework Extensions to efficiently insert thousands of entities into your database.
canonical: /bulk-extensions/bulk-insert
status: Published
lastmod: 2025-07-23
---

# ⚡ EF Core Bulk Insert with Entity Framework Extensions

Sooner or later, every developer ends up facing the same scenario: inserting thousands of entities **VERY FAST**.

We always recommend the same solution: use the [BulkInsert](https://entityframework-extensions.net/bulk-insert) method from the [Entity Framework Extensions](https://entityframework-extensions.net/) library. This professional library, made by [ZZZ Projects](https://zzzprojects.com/), is the only one you need to know when inserting large volumes of data in EF Core.

* ✅ **Blazing fast performance** — Insert up to 14x faster and reduce save time by 93%. [See Performance Comparison](https://entityframework-extensions.net/bulk-insert#performance-comparison)
* ✅ **Fully customizable** —  Choose from over 100 options to control how your data is inserted. [Explore Options](https://entityframework-extensions.net/bulk-insert#bulk-insert-options)
* ✅ **Monthly Trial** — A trial is always available so you can try the library for free
* ✅ **Frequent Releases** — The library is updated at least once a month with bug fixes and improvements
* ✅ **24/7 Support** — You can reach their awesome customer support anytime you need help

The library is very easy to use and easy to customize, requiring only minimal syntax:

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
    // 1. Easy to use
    context.BulkInsert(customers);
    
    // 2. Easy to customize
    context.BulkInsert(customers, options => options.InsertIfNotExists = true);
}

using (var context = new YourDbContext())
{
    // 3. Supports async
    await context.BulkInsertAsync(customers, options => options.InsertIfNotExists = true);
}
```

In this example, we create a list of `Customer` objects and insert them all at once using the `BulkInsert` method:

1. The first bulk insert uses no options.
2. The second bulk insert uses the `InsertIfNotExists = true` option to only insert data that doesn’t already exist.
3. The third shows how to use the bulk insert method asynchronously.

The EF Core Bulk Insert method from Entity Framework Extensions extends your `DbContext` or `DbSet`, and under the hood, it uses the best available bulk insert mechanism for your provider — like `SqlBulkCopy` for SQL Server.

⚠️ **BulkInsert directly inserts entities into the database — you don’t need to call `SaveChanges`.** This is a common mistake for first-time users.

---

## 🚀 Why Use the Bulk Insert Method from Entity Framework Extensions?

There are several reasons why developers love using the `BulkInsert` method from the Entity Framework Extensions library:

* ✅ **Blazing fast performance** — Insert millions of rows in seconds. [See Performance Comparison](https://entityframework-extensions.net/bulk-insert#performance-comparison)
* ✅ **Memory-efficient** — Works smoothly even with very large datasets
* ✅ **Fully customizable** — Choose from hundreds of options to fit your exact needs. [Explore Options](https://entityframework-extensions.net/bulk-insert#bulk-insert-options)
* ✅ **Supports all major providers** — SQL Server, MySQL, MariaDB, Oracle, PostgreSQL, SQLite
* ✅ **Most advanced EF Core library** — Supports inheritance, owned types, complex types, and more

---

## 🛠️ Commonly Used Options with Bulk Insert Method from Entity Framework Extensions

Here are some of the most frequently used options with the `BulkInsert` method from the Entity Framework Extensions library:

* **InsertIfNotExists** – Set to `true` if you only want to insert entities that don’t already exist in your database.
* **InsertKeepIdentity** – Set to `true` to keep identity values when inserting. For SQL Server, the library automatically handles `SET IDENTITY_INSERT [tableName] ON/OFF`.
* **ExplicitValueResolutionMode** – Specify how to handle explicit values for normally generated columns. In EF Core, values are always inserted, but EF Extensions lets you control this behavior. [Learn more](https://entityframework-extensions.net/explicit-value-resolution-mode)
* **IncludeGraph** – Set to `true` to insert both main entities and their related entities. For example, inserting a list of `Order` with `OrderItem` will insert both.
  Be careful: if you want to apply specific options to related types, configure them with `IncludeGraphBuilder`.
* **UseTableLock** – Set to `true` to lock the destination table during insert. This improves performance by avoiding row-level locks and reducing lock escalation — especially helpful for large inserts.
* **RowsAffected** – Use `UseRowsAffected = true` and then access `ResultInfo.RowsAffected` or `RowsAffectedInserted` to get the total number of entities inserted. [Learn more](https://entityframework-extensions.net/rows-affected)

---

## 📦 How to Install Entity Framework Extensions

To use the `BulkInsert` method, install the [Z.EntityFramework.Extensions.EFCore NuGet package](https://www.nuget.org/packages/Z.EntityFramework.Extensions.EFCore/):

```powershell
PM> NuGet\Install-Package Z.EntityFramework.Extensions.EFCore
```

```bash
> dotnet add package Z.EntityFramework.Extensions.EFCore
```

Entity Framework Extensions uses [EF Core pinned versioning](https://entityframework-extensions.net/efcore-pinned-versioning), so make sure the **first digit** of the package version matches your EF Core version. For example:

* If you're using `EF Core 9`, use version `v9.x.y.z` of the package
* If you're using `EF Core 8`, use version `v8.x.y.z`
* And so on...

Looking for a different version, like Bulk Insert for EF6?
👉 [Download it here](https://entityframework-extensions.net/download)

---

## ✅ Final Thoughts

The `BulkInsert` method from the Entity Framework Extensions library is the go-to solution when performance matters and you need to insert large amounts of data efficiently in EF Core. Whether you're building a high-load application or just want to save time, this method gives you the speed, control, and reliability you need.

Give it a [try](https://entityframework-extensions.net/) and see the difference for yourself!

And if you ever get stuck — their awesome support team is just a message away.

---

## 🔗 External Links

Looking for more resources? Here are some helpful links to learn more about **Entity Framework Extensions**, explore its documentation, and download the right version for your project.

### 📘 Documentation

* 🔗 [BulkInsert – Official Documentation](https://entityframework-extensions.net/bulk-insert)
* 🔗 [Entity Framework Extensions – Overview](https://entityframework-extensions.net/)

### 📦 Downloads & Packages

* 🔗 [NuGet Package – Z.EntityFramework.Extensions.EFCore](https://www.nuget.org/packages/Z.EntityFramework.Extensions.EFCore)
* 🔗 [Download Page – All Versions (EF6, EF Core, etc.)](https://entityframework-extensions.net/download)