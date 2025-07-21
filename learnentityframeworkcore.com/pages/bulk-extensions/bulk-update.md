---
title: EF Core Bulk Update with Entity Framework Extensions
description: Learn how to use the Bulk Update Extensions Methods from Entity Framework Extensions to update entities in the database using the fewest possible round-trips. This Bulk Extensions Method made by ZZZ Projects is especially useful to improve database performance and when you need to update using a custom key or take advantage of the many available options.
canonical: /bulk-extensions/bulk-update
status: Published
lastmod: 2025-07-19
---

# 🔄️ EF Core Bulk Update with Entity Framework Extensions

When using EF Core to update data, the usual process involves several steps:

1. **Load entities from the database into memory**  
   You typically do this using methods like `Find`, `FirstOrDefault`, or `Where` on the `DbSet` class.

2. **Modify the entity’s properties**  
   Once entities are loaded, you update their properties as needed.

3. **Call `SaveChanges()`**  
   After making your changes, you call `SaveChanges` on the `DbContext` to persist them to the database.

Here’s a basic example that updates multiple entities:

```csharp
var importCustomers = new List<Customer>
{
    new Customer { ID = 1, Name = "ZZZ Projects", City = "New York" },
    new Customer { ID = 2, Name = "Jonathan Magnan", City = "Montreal" }
    // Add more customers as needed
};

var importCustomersDict = importCustomers.ToDictionary(x => x.ID);

using (var context = new MyDbContext())
{
    // 1. Load entities from database into memory
    var customers = context.Customers
        .Where(x => importCustomersDict.ContainsKey(x.ID))
        .ToList();
    
    // 2. Modify the entity's properties
    foreach (var customer in customers)
    {
        var importCustomer = importCustomersDict[customer.ID];
        customer.Name = importCustomer.Name;
        customer.City = importCustomer.City;
    }

    // 3. Call SaveChanges
    context.SaveChanges();
}
```

This works fine for a small number of entities —
But what if you need to update **100,000 customers**?

That’s where [Bulk Update Extensions Methods](https://entityframework-extensions.net/bulk-update) from **Entity Framework Extensions** shines. It’s faster, cleaner, and skips all the heavy lifting:

* ❌ No need to load entities first
* ❌ No need to manually update properties

---

### ✅ Just one line does it all

By using the `BulkUpdate` method from [ZZZ Projects](https://zzzprojects.com/), you can skip steps 1 and 2 — and directly update your entities in the database:

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
    // Easy to use
    context.BulkUpdate(importCustomers, options =>
        options.ColumnInputExpression = x => new { x.Name, x.City });
}

using (var context = new MyDbContext())
{
    // Supports async
    await context.BulkUpdateAsync(importCustomers, options =>
        options.ColumnInputExpression = x => new { x.Name, x.City });
}
```

You’ve just transformed an inefficient, multi-step process into a **single line of code** that any developer can understand:

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore

context.BulkUpdate(importCustomers, options => 
    options.ColumnInputExpression = x => new { x.Name, x.City });
```

---

## 🚀 Why Use the Bulk Update Method from Entity Framework Extensions?

The `BulkUpdate` method from Entity Framework Extensions is the recommended solution when you need to update large sets of data efficiently. It optimizes database performance by reducing the number of queries, making your code cleaner, and giving you full control over how updates are applied.

**Bulk updating with Entity Framework Extensions is up to 4x faster**, reducing execution time by **75%** ([Online Benchmark](https://dotnetfiddle.net/ope4nq)).

* ✅ **Update the way you want** – Choose which properties to update, skip null values, apply conditions, and more.
* ✅ **Extremely fast** – Update thousands or even millions of rows in seconds. See the [Performance Comparison](https://entityframework-extensions.net/bulk-update#performance-comparison).
* ✅ **No need to load entities** – Save time and memory by skipping entity tracking.
* ✅ **Flexible with hundreds of options** – Fine-tune everything — batch size, conditions, behaviors — to match your business logic. Explore the [Bulk Update Options](https://entityframework-extensions.net/bulk-update#bulk-update-options).

---

## 📦 How to Install Entity Framework Extensions

To use the **Bulk Update Extensions Method** from **Entity Framework Extensions**, you need to install the [Z.EntityFramework.Extensions.EFCore NuGet Package](https://www.nuget.org/packages/Z.EntityFramework.Extensions.EFCore/):

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

Here’s a list of useful links to help you get started with **Entity Framework Extensions** and its **Bulk Extensions Methods**, such as `BulkUpdate`:

* 🔗 **Bulk Update:** [https://entityframework-extensions.net/bulk-update](https://entityframework-extensions.net/bulk-update)
* 🔗 **Entity Framework Extensions Overview:** [https://entityframework-extensions.net/](https://entityframework-extensions.net/)
* 🔗 **NuGet Package:** [https://www.nuget.org/packages/Z.EntityFramework.Extensions.EFCore](https://www.nuget.org/packages/Z.EntityFramework.Extensions.EFCore)
* 🔗 **ZZZ Projects (Official Site):** [https://zzzprojects.com/](https://zzzprojects.com/)
