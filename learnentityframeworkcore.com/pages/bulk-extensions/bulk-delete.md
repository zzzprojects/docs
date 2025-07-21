---
title: EF Core Bulk Delete with Entity Framework Extensions
description: Learn how to use the Bulk Delete Extensions Methods from Entity Framework Extensions to delete entities from the database using the fewest possible round-trips. This Bulk Extensions Method made by ZZZ Projects is especially useful to improve database performance and when you need to delete using a custom key or take advantage of the many available options.
canonical: /bulk-extensions/bulk-delete
status: Published
lastmod: 2025-07-19
---

# 🗑️ EF Core Bulk Delete with Entity Framework Extensions

EF Core provides the `Remove` and `RemoveRange` methods for deleting entities. However, they may not be the best option when deleting large amounts of data from a database.

The **fastest and easiest** way to delete multiple entities is by using the [Bulk Delete Extensions Method from Entity Framework Extensions](https://entityframework-extensions.net/bulk-delete), a third-party library built for EF Core. Imagine needing to delete 100,000 inactive customers — `BulkDelete` handles it in seconds.

This library, created by [ZZZ Projects](https://zzzprojects.com/), is extremely fast and offers hundreds of features — including all other bulk operations — to handle both common and complex scenarios.

To use the **Bulk Extensions Methods**, simply install the [Z.EntityFramework.Extensions.EFCore](https://www.nuget.org/packages/Z.EntityFramework.Extensions.EFCore/) NuGet package in your project and call the `BulkDelete` method from your `DbContext`.

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
    context.BulkDelete(customers);
    
    // Easy to customize
    context.BulkDelete(customers, options => options.BatchSize = 100);
}

using (var context = new YourDbContext())
{
    // Supports async
    await context.BulkDeleteAsync(customers);
}
````

---

## 🚀 Why Use the Bulk Delete Method from Entity Framework Extensions?

Deleting entities with EF Core is already fast.
But **bulk deleting with Entity Framework Extensions is up to 3x faster**, reducing execution time by **65%** ([Online Benchmark](https://dotnetfiddle.net/zzMQgZ)).

It also comes with several other advantages:

* ✅ **Delete the way you want** – Use custom keys, delete related entities (graph), or target specific conditions.
* ✅ **Extremely fast** – Delete thousands or even millions of rows in just seconds. See the [Performance Comparison](https://entityframework-extensions.net/bulk-delete#performance-comparison).
* ✅ **No need to load entities** – Avoid change tracking and delete directly from the database.
* ✅ **Flexible with hundreds of options** – Choose how relationships are handled, how keys are matched, and much more. Explore the [Bulk Delete Options](https://entityframework-extensions.net/bulk-delete#bulk-delete-options).

---

## 📦 How to Install Entity Framework Extensions

To use the **Bulk Delete Extensions Method** from **Entity Framework Extensions**, you need to install the [Z.EntityFramework.Extensions.EFCore NuGet Package](https://www.nuget.org/packages/Z.EntityFramework.Extensions.EFCore/):

```powershell
PM> NuGet\Install-Package Z.EntityFramework.Extensions.EFCore
```

```bash
> dotnet add package Z.EntityFramework.Extensions.EFCore
```

Make sure the **first number** of the version matches your EF Core version.
For example, if you’re using `EF Core 9`, then use version `v9.x.y.z` of the package.

Learn more about [EF Core Pinned Versioning conventions](https://entityframework-extensions.net/efcore-pinned-versioning).

For other options, such as EF6, visit the official downloads page:
👉 [https://entityframework-extensions.net/download](https://entityframework-extensions.net/download)

---

## 🔗 External Links

Here’s a list of useful links to help you get started with **Entity Framework Extensions** and its **Bulk Extensions Methods**, such as `BulkDelete`:

* 🔗 **Bulk Delete:** [https://entityframework-extensions.net/bulk-delete](https://entityframework-extensions.net/bulk-delete)
* 🔗 **Entity Framework Extensions Overview:** [https://entityframework-extensions.net/](https://entityframework-extensions.net/)
* 🔗 **NuGet Package:** [https://www.nuget.org/packages/Z.EntityFramework.Extensions.EFCore](https://www.nuget.org/packages/Z.EntityFramework.Extensions.EFCore)
* 🔗 **ZZZ Projects (Official Site):** [https://zzzprojects.com/](https://zzzprojects.com/)
