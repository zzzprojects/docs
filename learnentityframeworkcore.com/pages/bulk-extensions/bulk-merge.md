---
title: EF Core Bulk Merge (Upsert) with Entity Framework Extensions  
description: Learn how to use the Bulk Merge (Upsert – add or update) method from Entity Framework Extensions to efficiently merge entities in your database.  
canonical: /bulk-extensions/bulk-merge  
status: Published  
lastmod: 2025-07-21  
---

# 🧩 EF Core Bulk Merge / Upsert with Entity Framework Extensions

The [BulkMerge](https://entityframework-extensions.net/bulk-merge) method from [Entity Framework Extensions](https://entityframework-extensions.net/bulk-merge) is an `UPSERT` operation made for EF Core that combines both **insert** and **update** into a single, efficient database call.

* If the data **exists**, it will be updated
* If the data **doesn’t exist**, it will be inserted

This operation is also commonly called **upsert**, **add or update**, or **insert or update**.

EF Core doesn't offer a similar operation, so using the bulk merge method from Entity Framework Extensions is the only recommended solution.

In this example, we will merge two customers:

* The "ZZZ Projects" customer will be inserted since it doesn't exist
* The "Jonathan Magnan" customer will be inserted or updated depending on whether `ID = 13` is found in the database

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
    // Easy to use
    context.BulkMerge(customers);
    
    // Easy to customize
    context.BulkMerge(customers, options => options.IncludeGraph = true);
}

using (var context = new YourDbContext())
{
    // Supports async
    await context.BulkMergeAsync(customers);
}
```

---

## 🚀 Why Use the Bulk Merge Method from Entity Framework Extensions?

The `BulkMerge` method from Entity Framework Extensions is the recommended solution for EF Core when you need to add or update a large number of entities **very fast**.

Entity Framework Extensions is the **most popular** and **most complete** library available, with over **50 million downloads** and more than **5,000 paid customers**. Bulk merging in EF Core with Entity Framework Extensions is up to **5x faster**, reducing execution time by **up to 80%** ([Online Benchmark](https://dotnetfiddle.net/hmDtiI)).

Here’s why developers choose it:

* ✅ **Add or update the way you want** – Define custom keys, control which properties to insert or update, and apply conditions that fit your logic.
* ✅ **Extremely fast** – Handle thousands — even millions — of upserts in seconds. See the [Performance Comparison](https://entityframework-extensions.net/bulk-merge#performance-comparison).
* ✅ **No need to load entities** – Save memory and time by working directly with your data — no tracking or pre-loading required.
* ✅ **Flexible with hundreds of options** – Customize the behavior to match your business rules, from conditional updates to advanced key matching.
  [Explore Bulk Merge Options](https://entityframework-extensions.net/bulk-merge#bulk-merge-options)

---

## 🛠️ Commonly Used Options with the Bulk Merge Method from Entity Framework Extensions

Here are some of the most frequently used options with the `BulkMerge` method from the Entity Framework Extensions library:

* **OnMergeInsertInputExpression** – Use a lambda expression to choose which properties should be inserted during the insert phase of the merge operation. This option does **not** affect properties that will be updated.
* **OnMergeUpdateInputExpression** – Use a lambda expression to choose which properties should be updated during the update phase of the merge operation. This option does **not** affect properties that will be inserted.
* **IgnoreOnMergeInsertExpression** – Use a lambda expression to choose which properties should be **ignored** during the insert phase. All other properties will be inserted. This option does **not** affect the update phase.
* **IgnoreOnMergeUpdateExpression** – Use a lambda expression to choose which properties should be **ignored** during the update phase. All other properties will be updated. This option does **not** affect the insert phase.
* **IgnoreOnMergeInsert** – Set to `true` if you want to **skip the insert phase** of the merge operation.
* **IgnoreOnMergeUpdate** – Set to `true` if you want to **skip the update phase** of the merge operation.
* **IncludeGraph** – Set to `true` if you want to merge both the main entities and their related entities.
  For example, if you pass a list of `Order` that includes `OrderItem`, both will be merged.
  ⚠️ *Note*: If you want to apply specific options to a related entity type, you’ll need to configure them using `IncludeGraphBuilder`.
* **ColumnPrimaryKeyExpression** – Use a lambda expression to define which properties should be used as the primary key.
  Only rows that match this key will be updated — all others will be inserted.
* **RowsAffected** – Set `UseRowsAffected = true` to access merge result information. After the operation, you can check:
  * `ResultInfo.RowsAffected`
  * `ResultInfo.RowsAffectedInserted`
  * `ResultInfo.RowsAffectedUpdated`
    [Learn more](https://entityframework-extensions.net/rows-affected)

---

## 📦 How to Install Entity Framework Extensions

To use the bulk merge method, install the [Z.EntityFramework.Extensions.EFCore NuGet package](https://www.nuget.org/packages/Z.EntityFramework.Extensions.EFCore/):

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

Looking for a different version, like `BulkMerge` for EF6?
👉 [Download it here](https://entityframework-extensions.net/download)

---

## ✅ Final Thoughts

If you're still inserting or updating data using regular EF Core logic, it might be time to level up.

With just one line of code, the bulk merge method from Entity Framework Extensions lets you:

* Skip unnecessary reads
* Merge thousands of entities in seconds
* Fully control how inserts and updates behave

Whether you’re syncing records, importing external data, or cleaning up your database —
the bulk merge method gives you the **speed, flexibility, and simplicity** you've been missing.

Give it a [try](https://entityframework-extensions.net/bulk-merge) and see why over **5,000 companies** trust Entity Framework Extensions for their bulk operations. Once you use it, you'll wonder how you ever worked without it.

---

## 🔗 External Links

Looking for more resources? Here are some helpful links to learn more about the `BulkMerge` method and **Entity Framework Extensions**, explore its documentation, and download the right version for your project.

### 📘 Documentation

* 🔗 [Bulk Merge – Official Documentation](https://entityframework-extensions.net/bulk-merge)
* 🔗 [Entity Framework Extensions – Overview](https://entityframework-extensions.net/)

### 📦 Downloads & Packages

* 🔗 [NuGet Package – Z.EntityFramework.Extensions.EFCore](https://www.nuget.org/packages/Z.EntityFramework.Extensions.EFCore)
* 🔗 [Download Page – All Versions (EF6, EF Core, etc.)](https://entityframework-extensions.net/download)