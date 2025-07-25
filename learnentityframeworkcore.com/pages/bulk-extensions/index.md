---
title: Entity Framework Extensions: Bulk Insert, Update, Delete for EF Core
description: Learn how to use high-performance bulk operations in EF Core with Entity Framework Extensions. Easily speed up your .NET Core apps using methods like BulkInsert, BulkUpdate, BulkDelete, BulkRead, and BulkSaveChanges.
canonical: /bulk-extensions
status: Published
lastmod: 2025-07-22
---

# Bulk Extensions with EF Core from Entity Framework Extensions

Entity Framework Core (EF Core) is well-known as a great ORM (Object-Relational Mapping) tool. It makes saving entities to your database super easy—without writing any SQL statements.

But when you need to handle thousands of entities, EF Core’s performance can quickly become a bottleneck. That’s where the [bulk extension methods](https://entityframework-extensions.net/bulk-extensions) from the [Entity Framework Extensions](https://entityframework-extensions.net/) library come in. This powerful library dramatically boosts EF Core’s performance and gives you all the flexibility you need to save entities the way you want.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

// Easy to use
context.BulkInsert(customers);
context.BulkUpdate(customers);
context.BulkDelete(customers);
context.BulkMerge(customers);
context.BulkSynchronize(customers);

// Easy to customize
context.BulkInsert(customers, options => options.IncludeGraph = true);
```

---

## Why Use Bulk Extensions from Entity Framework Extensions?

[Entity Framework Extensions](https://zzzprojects.com/), created by **ZZZ Projects**, is the most popular and complete library for EF Core bulk operations — trusted by over **5,000 paid customers** and downloaded more than **50 million times**.

### ⚡ Performance That Speaks for Itself

One of the biggest advantages of using [Bulk Extensions from Entity Framework Extensions](https://entityframework-extensions.net/bulk-extensions) is the **massive performance boost** it brings when working with large datasets.

Here’s what kind of speed-up you can expect on **SQL Server**:

* 🔼 **Insert**: 14x faster — save up to **93%** of the time
  [View Benchmark](https://dotnetfiddle.net/cFWgKV)
* 🔼 **Update**: 4x faster — save up to **75%** of the time
  [View Benchmark](https://dotnetfiddle.net/ope4nq)
* 🔼 **Delete**: 3x faster — save up to **65%** of the time
  [View Benchmark](https://dotnetfiddle.net/zzMQgZ)

### 🚀 What Developers Love at First Glance

* ✅ **Supports all EF Core versions** — from the latest to legacy
* ✅ **Database agnostic** — Works with SQL Server, SQL Azure, PostgreSQL, MySQL, MariaDB, SQLite, and Oracle
* ✅ **Works with all model types** — Inheritance, complex types, owned types, value converters, and more
* ✅ **Fully customizable** — Over 100 options to control how your data is saved

### 🤝 What Keeps Them Coming Back

* ✅ **Frequent releases** — Updated monthly with new features and bug fixes
* ✅ **24/7 support** — Get help from our expert team anytime you need it
* ✅ **Monthly trial** — Try the full library for free with no commitment

---

Whether you're inserting millions of rows or just want more control and performance in your data layer, **Bulk Extensions from Entity Framework Extensions** makes your EF Core experience easier, faster, and smarter.

👉 [Give it a try](https://entityframework-extensions.net/) and see the difference for yourself.

---

## 📦 How to Install Entity Framework Extensions

To use the bulk extensions methods, install the [Z.EntityFramework.Extensions.EFCore NuGet package](https://www.nuget.org/packages/Z.EntityFramework.Extensions.EFCore/):

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

Looking for a different version, like `Bulk Extensions` methods for EF6?
👉 [Download it here](https://entityframework-extensions.net/download)

---

## EF Core Bulk Extensions with Entity Framework Extensions

Here is a list of some popular bulk extension methods offered by **Entity Framework Extensions**:

| Method                                                      | Description                                                                            |
| ----------------------------------------------------------- | -------------------------------------------------------------------------------------- |
| [Bulk Insert](/bulk-extensions/bulk-insert)                 | Inserts entities in bulk.                                                              |
| [Bulk Update](/bulk-extensions/bulk-update)                 | Updates entities in bulk.                                                              |
| [Bulk Delete](/bulk-extensions/bulk-delete)                 | Deletes entities in bulk.                                                              |
| [Bulk Merge](/bulk-extensions/bulk-merge)                   | Also known as **upsert**, **add or update**, or **insert or update**.                  |
| [Bulk Synchronize](/bulk-extensions/bulk-synchronize)       | **Mirror** operation that performs **insert**, **update**, and **delete** in one step. |
| [Where Bulk Contains](/bulk-extensions/where-bulk-contains) | Performs a `WHERE` clause in bulk, matching a list of items.                           |
| [Bulk SaveChanges](#ef-core-bulk-savechanges-extensions)    | Saves changes for multiple entities in a single bulk operation.                        |

---

## EF Core Bulk Insert with Entity Framework Extensions

The [`BulkInsert`](/bulk-extensions/bulk-insert) method from **Entity Framework Extensions** dramatically improves performance compared to `SaveChanges` when inserting multiple entities.

To perform a bulk insert, simply call the method with the list of entities you want to insert:

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

---

## EF Core Bulk Update with Entity Framework Extensions

The [`BulkUpdate`](/bulk-extensions/bulk-update) method from **Entity Framework Extensions** significantly improves performance compared to `SaveChanges` when you need to update multiple entities.

To perform a bulk update, simply call the method with the list of entities you want to update:

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

var importCustomers = new List<Customer>
{
    new Customer { ID = 1, Name = "ZZZ Projects", City = "New York" },
    new Customer { ID = 2, Name = "Jonathan Magnan", City = "Montreal" }
    // Add more customers as needed
};

using (var context = new MyDbContext())
{
    // 1. Easy to use
    context.BulkUpdate(importCustomers, options =>
        options.ColumnInputExpression = x => new { x.Name, x.City });
}

using (var context = new MyDbContext())
{
    // 2. Supports async
    await context.BulkUpdateAsync(importCustomers, options =>
        options.ColumnInputExpression = x => new { x.Name, x.City });
}
```

---

## EF Core Bulk Delete with Entity Framework Extensions

The [`BulkDelete`](/bulk-extensions/bulk-delete) method from Entity Framework Extensions improves performance when you need to delete multiple entities—especially compared to calling `SaveChanges()` for each one.

To perform a bulk delete, just call the method with the list of entities you want to remove.

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
```

---

## EF Core Bulk Merge with Entity Framework Extensions

The [`BulkMerge`](/bulk-extensions/bulk-merge) method from **Entity Framework Extensions** improves performance compared to `SaveChanges` when you need to insert **or** update multiple entities.

This is called an *upsert* operation — it updates existing entities and inserts new ones:

* If the data **exists**, it will be updated
* If the data **doesn’t exist**, it will be inserted

This operation is also commonly known as **upsert**, **add or update**, or **insert or update**.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

var customers = new List<Customer>
{
    new Customer { Name = "ZZZ Projects", City = "New York" },
    new Customer { ID = 13, Name = "Jonathan Magnan", City = "Montreal" }
    // Add more customers as needed
};

using (var context = new YourDbContext())
{
    // 1. Easy to use
    context.BulkMerge(customers);

    // 2. Easy to customize
    context.BulkMerge(customers, options => options.IncludeGraph = true);
}

using (var context = new YourDbContext())
{
    // 3. Supports async
    await context.BulkMergeAsync(customers);
}
```

---

## EF Core Bulk Synchronize with Entity Framework Extensions

The [`BulkSynchronize`](/bulk-extensions/bulk-synchronize) method from **Entity Framework Extensions** significantly improves performance compared to `SaveChanges` when you need to insert, update, **and** delete multiple entities.

This is called a *sync* operation — it ensures your database table matches the list you provide:

* If the data **exists in both** the source and target, it will be **updated**
* If the data **exists in the source but not in the target**, it will be **inserted**
* If the data **exists in the target but not in the source**, it will be **deleted**

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

var customers = new List<Customer>
{
    new Customer { Name = "ZZZ Projects", City = "New York" },
    new Customer { ID = 13, Name = "Jonathan Magnan", City = "Montreal" },
    new Customer { ID = 99, Name = "Invalid Customer", City = "XYZ" }
    // Add more customers as needed
};

using (var context = new YourDbContext())
{
    // 1. Easy to use
    context.BulkSynchronize(customers);

    // 2. Easy to customize
    context.BulkSynchronize(customers, options => options.BatchSize = 100);
}

using (var context = new YourDbContext())
{
    // 3. Supports async
    await context.BulkSynchronizeAsync(customers);
}
```

---

### EF Core WhereBulkContains with Entity Framework Extensions

The [`WhereBulkContains`](/bulk-extensions/where-bulk-contains) method from **Entity Framework Extensions** lets you filter a LINQ query using a large list of items — even when working with complex types, surrogate keys, or thousands of elements.

It works with basic lists, full entities, anonymous types, and more — and it's also compatible with other powerful features like `DeleteFromQuery`, `UpdateFromQuery`, and `BulkSaveChanges`.

### 🧠 Example – Load & Update Matching Entities

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

var dictDeserializedCustomers = deserializedCustomers.ToDictionary(x => x.CustomerID);

using (var context = new EntityContext())
{
    // 1. Filter from a list of existing items
    var customers = context.Customers
                           .WhereBulkContains(deserializedCustomers)
                           .ToList();

    // 2. Update values based on matched items
    customers.ForEach(x =>
    {
        var deserializedCustomer = dictDeserializedCustomers[x.CustomerID];

        x.Code = deserializedCustomer.Code;
        x.Email = deserializedCustomer.Email;
        x.FirstName = deserializedCustomer.FirstName;
        x.LastName = deserializedCustomer.LastName;
    });

    // 3. Save efficiently in bulk
    context.BulkSaveChanges();
}
```

### 🔥 Example – Use with DeleteFromQuery & UpdateFromQuery

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

var dictDeserializedCustomers = deserializedCustomers.ToDictionary(x => x.CustomerID);

using (var context = new EntityContext())
{
    // 1. Bulk delete matching entities
    context.Customers
           .WhereBulkContains(deserializedCustomers)
           .DeleteFromQuery();

    // 2. Bulk update matching entities
    context.Customers
           .WhereBulkContains(deserializedCustomers)
           .UpdateFromQuery(x => new Customer { IsActive = false });
}
```

---

### EF Core Bulk SaveChanges with Entity Framework Extensions

The [`BulkSaveChanges`](https://entityframework-extensions.net/bulk-savechanges) method from **Entity Framework Extensions** boosts performance compared to the regular `SaveChanges` method — especially when working with large numbers of tracked entities.

While EF Core’s `SaveChanges` has improved over time, performance can still drop when processing thousands of entities. That’s where `BulkSaveChanges` really shines — it reduces database round-trips and optimizes the save process in bulk.

To use it, simply replace your call to `SaveChanges` with `BulkSaveChanges`.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

using (var context = new YourDbContext())
{
    // Track your changes as usual
    context.Customers.Add(new Customer { Name = "ZZZ Projects" });
    context.Customers.First().IsActive = false;

    // 1. Just replace SaveChanges
    context.BulkSaveChanges();
}
```

---

## ✅ Final Thoughts

If you're still using EF Core’s regular `SaveChanges` for large operations, you're probably wasting time and resources.

The bulk extension methods from [Entity Framework Extensions](https://entityframework-extensions.net/) are simple to use, powerful to customize, and work across all major databases. Whether you want to insert, update, delete, read, or sync thousands of entities — there's a method designed to do it faster and smarter.

* No need to write raw SQL
* No need to load entities one by one
* No need to settle for slow performance

Give it a [try](https://entityframework-extensions.net/) and see why over **5,000 companies** already trust this library for high-performance EF Core operations.
Once you go bulk, you won't go back!

## 🔗 External Links

Looking for more resources? Here are some helpful links to learn more about the `BulkInsert` method and **Entity Framework Extensions**, explore its documentation, and download the right version for your project.

### 📘 Documentation

* 🔗 [Bulk Insert – Official Documentation](https://entityframework-extensions.net/bulk-insert)
* 🔗 [Bulk Update – Official Documentation](https://entityframework-extensions.net/bulk-update)
* 🔗 [Bulk Delete – Official Documentation](https://entityframework-extensions.net/bulk-delete)
* 🔗 [Bulk Merge – Official Documentation](https://entityframework-extensions.net/bulk-merge)
* 🔗 [Bulk Synchronize – Official Documentation](https://entityframework-extensions.net/bulk-synchronize)
* 🔗 [Where Bulk Contains – Official Documentation](https://entityframework-extensions.net/where-bulk-contains)
* 🔗 [Entity Framework Extensions – Overview](https://entityframework-extensions.net/)

### 📦 Downloads & Packages

* 🔗 [NuGet Package – Z.EntityFramework.Extensions.EFCore](https://www.nuget.org/packages/Z.EntityFramework.Extensions.EFCore)
* 🔗 [Download Page – All Versions (EF6, EF Core, etc.)](https://entityframework-extensions.net/download)
