---
title: EF Core Bulk Synchronize with Entity Framework Extensions
description: Learn how to use the Bulk Synchronize extension method from Entity Framework Extensions to insert, update, or delete entities in the database using the fewest possible round-trips. Created by ZZZ Projects, this method is perfect for boosting performance and applying custom merge logic using a single operation.
canonical: /bulk-extensions/bulk-synchronize
status: Published
lastmod: 2025-07-23
---

# EF Core Bulk Synchronize with Entity Framework Extensions

[Entity Framework Extensions](https://entityframework-extensions.net/) provides the `BulkSynchronize` extension method, which allows you to perform bulk inserts, updates, and deletes in a single operation.

- ✅ It reduces the need for writing multiple separate queries.
- ✅ It improves performance by batching commands to minimize database round-trips.
- ✅ It supports advanced features like conditional synchronization for more control over how data is merged.

Using `BulkSynchronize` is simple. Just pass your list of entities and let the library handle the rest. Properties will be automatically mapped to matching column names.

Here’s an example of how to use the `BulkSynchronize` method with EF Core:

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
    context.BulkSynchronize(customers);

    // Easy to customize
    context.BulkSynchronize(customers, options => options.IncludeGraph = true);
}

using (var context = new YourDbContext())
{
    // Supports async
    await context.BulkSynchronizeAsync(customers);
}
````

Bulk synchronization is especially useful when you need to keep two datasets in sync or handle large-scale data operations efficiently.
Instead of inserting, updating, and deleting entities one by one, everything is handled in a single, optimized batch.

The **easiest and fastest way** to synchronize data with EF Core is by using the [Entity Framework Extensions](https://entityframework-extensions.net/) library from ZZZ Projects.

---

## 🚀 Why Use the Bulk Synchronize Method from Entity Framework Extensions?

The `BulkSynchronize` method is a smart choice when you want to keep data consistent and improve performance by reducing round-trips to the database.

Here are a few reasons to use it:

* ✅ Ensures **data consistency** by executing all operations in a single transaction
* ✅ **Much faster** than performing separate insert, update, and delete operations
* ✅ Ideal for large-scale operations such as **data migrations** or **data sync between systems**
* ✅ Supports advanced features like **custom keys** and **conditional sync logic**

---

## 📦 How to Install Entity Framework Extensions

To use the `BulkSynchronize` method, install the [Z.EntityFramework.Extensions.EFCore NuGet package](https://www.nuget.org/packages/Z.EntityFramework.Extensions.EFCore/):

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

Here are some useful links to help you get started with **Entity Framework Extensions** and its **Bulk Extension methods**, like `BulkSynchronize`:

* 🔗 [Bulk Synchronize Documentation](https://entityframework-extensions.net/bulk-synchronize)
* 🔗 [Entity Framework Extensions Overview](https://entityframework-extensions.net/)
* 🔗 [NuGet Package](https://www.nuget.org/packages/Z.EntityFramework.Extensions.EFCore)
* 🔗 [ZZZ Projects (Official Site)](https://zzzprojects.com/)
