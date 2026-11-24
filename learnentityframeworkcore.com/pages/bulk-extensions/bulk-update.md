---
title: EF Core Bulk Update with Entity Framework Extensions
description: Learn how to use the EF Core Bulk Update method from Entity Framework Extensions to efficiently update thousands of entities in your database.
canonical: /bulk-extensions/bulk-update
status: Published
lastmod: 2025-11-11
---

# 🔄️ EF Core Bulk Update with Entity Framework Extensions

When using EF Core to update data, the typical process involves several steps:

1. **Load entities from the database into memory**
   This is usually done using methods like `Find`, `FirstOrDefault`, or `Where` on the `DbSet` class.

2. **Modify the entity’s properties**
   Once the entities are loaded, you update their properties as needed.

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
    // 1. Load entities from the database into memory
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

This approach works fine for a small number of entities — but what if you need to update **10,000 customers**?

That’s where the [BulkUpdate](https://entityframework-extensions.net/bulk-update) method from the [Entity Framework Extensions](https://entityframework-extensions.net/) library shines. It’s faster, cleaner, and skips all the heavy lifting:

* ❌ No need to load entities first
* ❌ No need to manually update properties
* ✅ Just update your entities directly

By using the BulkUpdate method from [ZZZ Projects](https://zzzprojects.com/), you can skip steps 1 and 2 — and update your entities directly in the database:

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

You’ve just turned an inefficient, multi-step update into a **single line of code** that any developer can understand:

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

context.BulkUpdate(importCustomers, options =>
    options.ColumnInputExpression = x => new { x.Name, x.City });
```

In this example, all imported customers will be updated, and only the `Name` and `City` properties will be affected.

---

## 🚀 Why Use the Bulk Update Method from Entity Framework Extensions?

The bulk update method from Entity Framework Extensions is the recommended solution for EF Core when you need to update large sets of data efficiently.

Entity Framework Extensions is the **most popular** and **most complete** library available, with over **50 million downloads** and more than **5,000 paid customers**. Bulk updating in EF Core with Entity Framework Extensions is up to **4x faster**, reducing execution time by **75%** ([Online Benchmark](https://dotnetfiddle.net/ope4nq)).

It helps you optimize database performance by reducing the number of queries, making your code cleaner, and giving you full control over how updates are applied:

* ✅ **Update the way you want** — Choose which properties to update, skip null values, apply conditions, and more.
* ✅ **Extremely fast** — Update thousands or even millions of rows in seconds. See the [Performance Comparison](https://entityframework-extensions.net/bulk-update#performance-comparison).
* ✅ **No need to load entities** — Save time and memory by skipping entity tracking.
* ✅ **Flexible with hundreds of options** — Fine-tune everything: batch size, conditions, behaviors, and more. Explore the [Bulk Update Options](https://entityframework-extensions.net/bulk-update#bulk-update-options).
* ✅ **Supports all major providers** — SQL Server, MySQL, MariaDB, Oracle, PostgreSQL, SQLite.
* ✅ **Most advanced EF Core library** — Supports inheritance, owned types, complex types, and more.

---

## 🛠️ Commonly Used Options with the the Bulk Update Method from Entity Framework Extensions

Here are some of the most frequently used options with the `BulkUpdate` method from the [Entity Framework Extensions](https://entityframework-extensions.net/) library:

* **`ColumnPrimaryKeyExpression`** – Choose which properties to use as the primary key by providing a lambda expression. Only rows that match the key will be updated.
* **`ColumnInputExpression`** – Select which properties should be updated by using a lambda expression. All other properties will be ignored.
* **`IgnoreOnUpdateExpression`** – Specify which properties should be ignored during the update using a lambda expression. All other properties will be included.
* **`UpdateMatchedAndConditionExpression`** – After matching rows by primary key, you can add extra conditions using a lambda expression. All specified property values must match between the entity and the database for the update to happen.
* **`UpdateMatchedAndOneNotConditionExpression`** – Similar to the above, but only **one** of the specified property values needs to be different between the entity and the database for the row to be updated.
* **`IncludeGraph`** – Set this to `true` if you want to update both the main entities and their related entities. For example, if you pass a list of `Order` that includes `OrderItem`, both will be updated.
  ⚠️ Be careful — if you want to apply specific options to a related entity type, you’ll need to configure them using `IncludeGraphBuilder`.
* **`UseRowsAffected`** – Set this to `true` if you want to know how many rows were affected after the update. You can then access `ResultInfo.RowsAffected` or `ResultInfo.RowsAffectedUpdated`.
  👉 [Learn more](https://entityframework-extensions.net/rows-affected)

---

## 📦 How to Install Entity Framework Extensions

To use the bulk update method, install the [Z.EntityFramework.Extensions.EFCore NuGet package](https://www.nuget.org/packages/Z.EntityFramework.Extensions.EFCore/):

```powershell
PM> NuGet\Install-Package Z.EntityFramework.Extensions.EFCore
```

```bash
> dotnet add package Z.EntityFramework.Extensions.EFCore
```

Entity Framework Extensions uses [EF Core pinned versioning](https://entityframework-extensions.net/efcore-pinned-versioning), so make sure the **first digit** of the package version matches your EF Core version. For example:

* If you're using `EF Core 11`, use version `v11.x.y.z`
* If you're using `EF Core 10`, use version `v10.x.y.z`
* If you're using `EF Core 9`, use version `v9.x.y.z`
* If you're using `EF Core 8`, use version `v8.x.y.z`
* And so on...

Looking for a different version, like `BulkUpdate` for EF6?
👉 [Download it here](https://entityframework-extensions.net/download)

---

## ✅ Final Thoughts

If you're still updating large numbers of rows using regular EF Core logic, it's time to make your life easier.

With just one line of code, the bulk update method from Entity Framework Extensions lets you:

* Skip unnecessary steps
* Dramatically boost performance
* Customize everything to fit your needs

Whether you're dealing with 10 or 10 million rows, the bulk update method from Entity Framework Extensions gives you the speed, control, and simplicity you've been looking for.

Give it a [try](https://entityframework-extensions.net/bulk-update) and see why over **5,000 companies** already trust Entity Framework Extensions to handle their bulk operations. Once you use it, you won’t want to go back!

---

## 🔗 External Links

Looking for more resources? Here are some helpful links to learn more about the `BulkUpdate` method and **Entity Framework Extensions**, explore its documentation, and download the right version for your project.

### 📘 Documentation

* 🔗 [Bulk Update – Official Documentation](https://entityframework-extensions.net/bulk-update)
* 🔗 [Entity Framework Extensions – Overview](https://entityframework-extensions.net/)

### 📦 Downloads & Packages

* 🔗 [NuGet Package – Z.EntityFramework.Extensions.EFCore](https://www.nuget.org/packages/Z.EntityFramework.Extensions.EFCore)
* 🔗 [Download Page – All Versions (EF6, EF Core, etc.)](https://entityframework-extensions.net/download)