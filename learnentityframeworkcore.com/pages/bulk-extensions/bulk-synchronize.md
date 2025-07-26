---
title: EF Core Bulk Synchronize with Entity Framework Extensions  
description: Learn how to use the Bulk Synchronize method (Add, Update, Delete) from Entity Framework Extensions to efficiently sync your entities with the database.  
canonical: /bulk-extensions/bulk-synchronize  
status: Published  
lastmod: 2025-07-23
---

# EF Core Bulk Synchronize with Entity Framework Extensions

Imagine you have a list of entities and you want to mirror it with your database table using EF Core. How would you proceed? EF Core doesn't offer a built-in method for this behavior, and handling all the logic yourself will make your code much more complex.

The [BulkSynchronize](https://entityframework-extensions.net/bulk-synchronize) method from [Entity Framework Extensions](https://entityframework-extensions.net/) is a **mirror** operation designed for EF Core that combines **insert**, **update**, and **delete** operations in one step:

* If the data **exists** in both the source and the target, it will be updated.
* If the data **doesn’t exist** in the target, it will be inserted.
* If the data **doesn’t exist** in the source, it will be deleted.

Using `BulkSynchronize` with Entity Framework Extensions in EF Core is simple. Just pass your list of entities and let the library handle the rest:

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
    // Easy to use
    context.BulkSynchronize(customers);

    // Easy to customize
    context.BulkSynchronize(customers, options => options.BatchSize = 100);
}

using (var context = new YourDbContext())
{
    // Supports async
    await context.BulkSynchronizeAsync(customers);
}
```

---

## 🚀 Why Use the Bulk Synchronize Method from Entity Framework Extensions?

The `BulkSynchronize` method is a smart choice when you want to keep your data consistent while improving performance by reducing round-trips to the database.

**Entity Framework Extensions** is the **most popular** and **most complete** library available, with over **50 million downloads** and more than **5,000 paid customers**.

Here are a few reasons to use it:

* ✅ Ensures **data consistency** by executing all operations in a single transaction
* ✅ **Much faster** than running separate insert, update, and delete statements
* ✅ Perfect for large-scale scenarios like **data migrations** or **synchronizing systems**
* ✅ Supports advanced features like **custom keys** and **conditional sync rules**

---

## 🛠️ Commonly Used Options with the Bulk Synchronize Method from Entity Framework Extensions

Here are some of the most frequently used options when working with the `BulkSynchronize` method from the Entity Framework Extensions library:

* **ColumnSynchronizeDeleteKeySubsetExpression** – Use a lambda expression to define an additional key so that only a *subset* of the table is synchronized, instead of the entire table.
* **SynchronizeSoftDeleteFormula** – Use a hardcoded SQL statement to define how a row should be updated for a soft delete, instead of being physically removed.
* **ColumnPrimaryKeyExpression** – Use a lambda expression to choose which properties make up the key. Only rows matching this key will be updated; all others will be inserted.
* **OnSynchronizeInsertInputExpression** – Use a lambda expression to choose which properties should be inserted during the **insert phase** of the synchronize operation. This doesn't affect updates.
* **OnSynchronizeUpdateInputExpression** – Use a lambda expression to choose which properties should be updated during the **update phase** of the synchronize operation. This doesn't affect inserts.
* **IgnoreOnSynchronizeInsertExpression** – Use a lambda expression to specify which properties to ignore during the insert phase. All others will be inserted. This does not affect the update phase.
* **IgnoreOnSynchronizeUpdateNames** – Use a list of property names (as strings) to specify which properties to ignore during the update phase. All others will be updated. This does not affect the insert phase.
* **RowsAffected** – Set `UseRowsAffected = true`, then access `ResultInfo.RowsAffected`, `RowsAffectedInserted`, `RowsAffectedUpdated`, `RowsAffectedDeleted`, or `RowsAffectedSoftDeleted` to see how many entities were added, updated, deleted, or soft-deleted.
  [Learn more](https://entityframework-extensions.net/rows-affected).

## 📦 How to Install Entity Framework Extensions

To use the bulk Synchronize method, install the [Z.EntityFramework.Extensions.EFCore NuGet package](https://www.nuget.org/packages/Z.EntityFramework.Extensions.EFCore/):

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

Looking for a different version, like `BulkSynchronize` for EF6?
👉 [Download it here](https://entityframework-extensions.net/download)

## ✅ Final Thoughts

If you’ve ever struggled with syncing large datasets manually using EF Core, `BulkSynchronize` is the solution you’ve been waiting for.

With just one method, you can insert, update, and delete — all in a single, efficient operation. You save time, reduce complexity, and improve performance.

Whether you’re handling a one-time data migration or keeping systems in sync every day, the `BulkSynchronize` method from Entity Framework Extensions gives you the power and flexibility to do it right.

Give it a [try](https://entityframework-extensions.net/bulk-synchronize) and see why thousands of developers and companies trust it for their bulk operations. Once you use it, you’ll never go back to writing manual sync logic again.

---

## 🔗 External Links

Looking for more resources? Here are some helpful links to learn more about the `BulkSynchronize` method and **Entity Framework Extensions**, explore its documentation, and download the right version for your project.

### 📘 Documentation

* 🔗 [Bulk Synchronize – Official Documentation](https://entityframework-extensions.net/bulk-synchronize)
* 🔗 [Entity Framework Extensions – Overview](https://entityframework-extensions.net/)

### 📦 Downloads & Packages

* 🔗 [NuGet Package – Z.EntityFramework.Extensions.EFCore](https://www.nuget.org/packages/Z.EntityFramework.Extensions.EFCore)
* 🔗 [Download Page – All Versions (EF6, EF Core, etc.)](https://entityframework-extensions.net/download)