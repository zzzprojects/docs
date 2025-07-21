---
title: EF Core Bulk Merge / Upsert with Entity Framework Extensions
description: Learn how to use the Bulk Merge (Upsert) extension method from Entity Framework Extensions to merge entities in the database using the fewest possible round-trips. This method, created by ZZZ Projects, is especially useful for improving database performance or when you need to merge using a custom key or take advantage of the many available options.
canonical: /bulk-extensions/bulk-merge
status: Published
lastmod: 2025-07-21
---

# 🧩 EF Core Bulk Merge / Upsert with Entity Framework Extensions

The `BulkMerge` method from [Entity Framework Extensions](https://entityframework-extensions.net/bulk-merge) is an `UPSERT` operation that combines both **insert** and **update** into a single, efficient database call.

- If the data **exists**, it will be updated.  
- If the data **doesn’t exist**, it will be inserted.

This operation is often also called **upsert**, **add or update**, or **insert or update**.

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
    context.BulkMerge(customers);
    
    // Easy to customize
    context.BulkMerge(customers, options => options.IncludeGraph = true);
}

using (var context = new YourDbContext())
{
    // Supports async
    await context.BulkMergeAsync(customers);
}
````

---

## 🚀 Why Use the Bulk Merge Method from Entity Framework Extensions?

The `BulkMerge` method is the recommended solution when you need to perform an `add or update` operation. **Bulk merging with Entity Framework Extensions is up to 5x faster**, reducing execution time by **80%** ([Online Benchmark](https://dotnetfiddle.net/hmDtiI)).

It also comes with several other advantages:

✅ **Add or update the way you want:** Define custom keys, control which properties to insert or update, and apply conditions.
✅ **Extremely fast:** Handle thousands or even millions of upserts in seconds. See the [Performance Comparison](https://entityframework-extensions.net/bulk-merge#performance-comparison).
✅ **No need to load entities:** Save memory and time by working directly with your data — no tracking required.
✅ **Flexible with hundreds of options:** Customize the behavior to match your business rules — from conditional updates to advanced key matching. Explore the full list of [Bulk Merge Options](https://entityframework-extensions.net/bulk-merge#bulk-merge-options).

---

## 📦 How to Install Entity Framework Extensions

To use the **Bulk Merge extension method**, install the [Z.EntityFramework.Extensions.EFCore NuGet Package](https://www.nuget.org/packages/Z.EntityFramework.Extensions.EFCore/):

```powershell
PM> NuGet\Install-Package Z.EntityFramework.Extensions.EFCore
```

```bash
> dotnet add package Z.EntityFramework.Extensions.EFCore
```

Make sure the **first digit** of the version matches your EF Core version.
For example, if you’re using `EF Core 9`, use version `v9.x.y.z` of the package.

Learn more about [EF Core pinned versioning](https://entityframework-extensions.net/efcore-pinned-versioning).

Need another version like EF6? Visit the official downloads page:
👉 [https://entityframework-extensions.net/download](https://entityframework-extensions.net/download)

---

## 🔗 External Links

Here are some useful links to help you get started with **Entity Framework Extensions** and its **Bulk Extensions Methods**, like `BulkMerge`:

* 🔗 **Bulk Merge:** [https://entityframework-extensions.net/bulk-merge](https://entityframework-extensions.net/bulk-merge)
* 🔗 **Entity Framework Extensions Overview:** [https://entityframework-extensions.net/](https://entityframework-extensions.net/)
* 🔗 **NuGet Package:** [https://www.nuget.org/packages/Z.EntityFramework.Extensions.EFCore](https://www.nuget.org/packages/Z.EntityFramework.Extensions.EFCore)
* 🔗 **ZZZ Projects (Official Site):** [https://zzzprojects.com/](https://zzzprojects.com/)
