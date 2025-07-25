---
title: EF Core Bulk Delete with Entity Framework Extensions  
description: Learn how to use the Bulk Delete extension methods from Entity Framework Extensions to delete entities from the database without tracking them in the DbContext.  
canonical: /bulk-extensions/bulk-delete  
status: Published  
lastmod: 2025-07-19  
---

# 🗑️ EF Core Bulk Delete with Entity Framework Extensions

EF Core provides the `Remove` and `RemoveRange` methods for deleting entities. 

However, to use them, you first need to retrieve those entities from the `DbContext`, which takes time — and often doesn’t make sense.  
Why would you retrieve something you just want to delete anyway?

The **fastest and easiest** way to delete multiple entities is by using the [BulkDelete](https://entityframework-extensions.net/bulk-delete) method from [Entity Framework Extensions](https://entityframework-extensions.net/).

This third-party library for EF Core, created by [ZZZ Projects](https://zzzprojects.com/), is extremely fast and offers hundreds of options — including all other bulk operations — to handle both common and complex scenarios.

In this example, we’ll create a customer list, only specify the `ID`, and call the delete method:
- without options  
- with options  
- and asynchronously

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

var customersToDelete = new List<Customer>
{
    new Customer { ID = 1 },
    new Customer { ID = 13 }
    // Add more customers as needed
};

using (var context = new YourDbContext())
{
    // Easy to use
    context.BulkDelete(customersToDelete);
    
    // Easy to customize
    context.BulkDelete(customersToDelete, options => options.BatchSize = 100);
}

using (var context = new YourDbContext())
{
    // Supports async
    await context.BulkDeleteAsync(customersToDelete);
}
````

---

## 🚀 Why Use the Bulk Delete Method from Entity Framework Extensions?

The bulk delete method from Entity Framework Extensions is the recommended solution for EF Core when you need to delete a large number of entities **very fast**.

Entity Framework Extensions is the **most popular** and **most complete** library available, with over **50 million downloads** and more than **5,000 paid customers**. Bulk deleting in EF Core with Entity Framework Extensions is up to **3x faster**, reducing execution time by **65%** ([Online Benchmark](https://dotnetfiddle.net/zzMQgZ)).

It also comes with several other advantages:

- ✅ **No need to load entities** – Avoid change tracking and delete directly from the database.
- ✅ **Delete the way you want** – Use custom keys, delete related entities (graph), or target specific conditions.
- ✅ **Extremely fast** – Delete thousands or even millions of rows in just seconds. See the [Performance Comparison](https://entityframework-extensions.net/bulk-delete#performance-comparison).
- ✅ **Flexible with hundreds of options** – Choose how relationships are handled, how keys are matched, and much more. Explore the [Bulk Delete Options](https://entityframework-extensions.net/bulk-delete#bulk-delete-options).
- ✅ **Supports all major providers** – SQL Server, MySQL, MariaDB, Oracle, PostgreSQL, SQLite.
- ✅ **Most advanced EF Core library** – Supports inheritance, owned types, complex types, and more.

---

## 🛠️ Commonly Used Options with the Bulk Delete Method from Entity Framework Extensions

Here are some of the most frequently used options with the bulk delete method from the Entity Framework Extensions library:

- **ColumnPrimaryKeyExpression** – Choose which properties should be used as the key by using a lambda expression. Only rows that match this key will be deleted.
- **DeletePrimaryKeyAndFormula** – Specify a hardcoded SQL formula to include additional logic—along with the primary key—to determine if the entity matches a row in the database. Only rows that also match this formula will be deleted.
- **Batch** – Customize `BatchSize`, `BatchTimeout`, and `BatchDelayInterval` to improve performance and control how deleted entities are grouped and executed.
- **IncludeGraph** – Set to `true` if you want to delete both the main entities and their related entities.  
  For example, if you pass a list of `Order` that includes `OrderItem`, both will be deleted.  
  ⚠️ Be careful: if you want to apply specific options to related entity types, you’ll need to configure them using `IncludeGraphBuilder`.
- **RowsAffected** – Set `UseRowsAffected = true` to access the number of deleted entities through `ResultInfo.RowsAffected` or `ResultInfo.RowsAffectedDeleted`.  
  [Learn more](https://entityframework-extensions.net/rows-affected)

---

## 📦 How to Install Entity Framework Extensions

To use the bulk delete method, install the [Z.EntityFramework.Extensions.EFCore NuGet package](https://www.nuget.org/packages/Z.EntityFramework.Extensions.EFCore/):

```powershell
PM> NuGet\Install-Package Z.EntityFramework.Extensions.EFCore
```

```bash
> dotnet add package Z.EntityFramework.Extensions.EFCore
```

Entity Framework Extensions uses [EF Core pinned versioning](https://entityframework-extensions.net/efcore-pinned-versioning), so make sure the **first digit** of the package version matches your EF Core version. For example:

* If you're using `EF Core 9`, use version `v9.x.y.z`
* If you're using `EF Core 8`, use version `v8.x.y.z`
* And so on...

Looking for a different version, like `BulkDelete` for EF6?
👉 [Download it here](https://entityframework-extensions.net/download)

---

## ✅ Final Thoughts

When performance matters and you need to delete entities in bulk—without the overhead of tracking, loading, or writing raw SQL—the bulk delete method from Entity Framework Extensions is the way to go.

It’s fast, easy to use, and flexible enough to handle even the most advanced EF Core scenarios.

Whether you're cleaning up old data, syncing large datasets, or optimizing background jobs.

Give it a [try](https://entityframework-extensions.net/bulk-delete) and start deleting smarter, not slower.

---

## 🔗 External Links

Looking for more resources? Here are some helpful links to learn more about the `BulkDelete` method and **Entity Framework Extensions**, explore its documentation, and download the right version for your project.

### 📘 Documentation

* 🔗 [Bulk Delete – Official Documentation](https://entityframework-extensions.net/bulk-delete)
* 🔗 [Entity Framework Extensions – Overview](https://entityframework-extensions.net/)

### 📦 Downloads & Packages

* 🔗 [NuGet Package – Z.EntityFramework.Extensions.EFCore](https://www.nuget.org/packages/Z.EntityFramework.Extensions.EFCore)
* 🔗 [Download Page – All Versions (EF6, EF Core, etc.)](https://entityframework-extensions.net/download)