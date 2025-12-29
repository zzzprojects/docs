---
Title: Entity Framework DeleteFromQuery – Delete Rows Directly from LINQ
MetaDescription: Understand how Entity Framework DeleteFromQuery deletes database rows directly from LINQ, without loading entities, tracking changes, or SaveChanges.
LastMod: 2025-12-27
---

# Entity Framework DeleteFromQuery

## What is DeleteFromQuery

`DeleteFromQuery` is a **FREE** method supported by both **EF Core** and **EF6** (see also [InsertFromQuery](/insert-from-query) and [UpdateFromQuery](/update-from-query)). It lets you delete database rows **directly in SQL**:

* Without **reading data** or loading entities into memory
* Without using the **Change Tracker**
* Without calling `SaveChanges`

Think of `DeleteFromQuery` as a **SQL DELETE statement generated from LINQ**, executed as a **set-based operation**, not row by row.

You should use `DeleteFromQuery` when you want to **delete rows from a database table that can be represented by a LINQ query**. If you want to delete entities instead, you should use [BulkDelete](/bulk-delete).

`DeleteFromQuery` is available in both synchronous and asynchronous versions:

* `DeleteFromQuery`
* `DeleteFromQueryAsync`

```csharp
// DELETE all customers that are inactive
context.Customers
    .Where(x => !x.IsActive)
    .DeleteFromQuery();

// DELETE customers by id
context.Customers
    .Where(x => x.ID == userId)
    .DeleteFromQuery();
```

[Try it in EF Core](https://dotnetfiddle.net/nGIOTQ) | [Try it in EF6](https://dotnetfiddle.net/0my0fe)

In short, it tells the database: **“DELETE all matching rows like this.”**

## DeleteFromQuery BatchDeleteManager

The `BatchDeleteManager` lets you configure **global options** for `DeleteFromQuery`.

All options defined here are applied **automatically to every `DeleteFromQuery` call**, across your entire application.

You can use it when you want **consistent behavior everywhere**, without repeating configuration on each delete.

Because these options are global, any change will affect **all future `DeleteFromQuery` executions**.

The `BatchDeleteManager` has 3 options:
- [BatchDeleteBuilder](#batchdeletebuilder)
- [InMemoryDbContextFactory](#inmemorydbcontextfactory)

### BatchDeleteBuilder

The `BatchDeleteBuilder` is an action that lets you set options that will be applied to **every delete** executed with the `DeleteFromQuery` method.

This configuration is global and affects **all `DeleteFromQuery` calls**, regardless of the context or query.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

BatchDeleteManager.BatchUpdateBuilder = builder =>
{
    builder.BatchSize = 1000;
};
```

You can use this when you want to enforce the same batch configuration everywhere and avoid repeating options in each call.

### InMemoryDbContextFactory

If you use an **InMemory** provider, you must provide the in-memory context where the logic will be applied.

This configuration is global and will be used by **all `DeleteFromQuery` calls** that run against an in-memory provider.

This is required because we apply some internal logic that could affect your original context. For example, we may need to attach entities in order to remove them.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

BatchDeleteManager.InMemoryDbContextFactory = () => new MyMemoryContext(options);
```

You only need to configure this once for the entire application.

## DeleteFromQuery Options

You can also set options **directly inside an `DeleteFromQuery` call**.

These options apply **only to the current execution**, unlike `BatchDeleteManager`, which configures global behavior.

We support the following options:

* [BatchSize](#batchsize)
* [BatchDelayInterval](#batchdelayinterval)
* [CommandExecuteAction](#commandexecuteaction)
* [Executing](#executing)
* [IgnoreInMemoryAsNoTracking](#ignoreinmemoryasnotracking)
* [InMemoryDbContextFactory](#inmemorydbcontextfactory)
* [UseTableLock](#usetablelock)

### BatchSize

Allows you to specify **how many rows are delete at a time**.

This option is supported **only for SQL Server**.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

context.Customers
    .Where(x => !x.IsActive)
    .DeleteFromQuery(options => options.BatchSize = 1000);
```

You can use this option to reduce lock duration when updating a very large number of rows.

### BatchDelayInterval

Allows you to **wait a specified number of milliseconds between batch deletes**, not before the first batch is executed.

This option **only works with SQL Server** and uses the SQL statement `WAITFOR DELAY '{BatchDelayInterval}'`.

You can use this option to **throttle large delete operations**, helping to reduce database pressure, avoid long locks, and limit transaction log growth.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

context.Customers
    .Where(x => !x.IsActive)
    .DeleteFromQuery(options => options.BatchDelayInterval = 2000);
```

### CommandExecuteAction

Allows you to override **how the command is executed**.

For example, you can log the command text before execution, execute it manually, or wrap it in custom logic.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

context.Customers
    .Where(x => !x.IsActive)
    .DeleteFromQuery(
        options => options.CommandExecuteAction = (command) =>
        {
            Console.WriteLine(command.CommandText);
            return command.ExecuteNonQuery();
        }
    );
```

### Executing

The `Executing` action is called **just before the command is executed** and before interceptors run.

It is often useful when you want to **modify the command text**, such as changing table names or adding hints dynamically.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

context.Customers
    .Where(x => !x.IsActive)
    .DeleteFromQuery(
        options => options.Executing = command =>
        {
            command.CommandText = command.CommandText.Replace("[MyTableName]", "[AnotherTableName]");
        }
    );
```

### IgnoreInMemoryAsNoTracking

When enabled, the method will use `AsNoTracking` when materializing the list of entities to delete.

This option is useful if you do not want entities to be loaded or tracked in your original context.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

context.Customers
    .Where(x => !x.IsActive)
    .DeleteFromQuery(options => options.IgnoreInMemoryAsNoTracking = true);
```

### InMemoryDbContextFactory

This option is the same as the `BatchDeleteManager.InMemoryDbContextFactory` global option but this time at instance level.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

context.Customers
    .Where(x => !x.IsActive)
    .DeleteFromQuery(options => options.InMemoryDbContextFactory = () => new MyMemoryContext(options));
```

### UseTableLock

When enabled, the query is executed with a **table lock hint**.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

context.Customers
    .Where(x => !x.IsActive)
    .DeleteFromQuery(options => options.UseTableLock = true);
```

## FAQ

### What is the difference with EF Core ExecuteDelete?

For people using **EF Core 7+**, a similar built-in method called [`ExecuteDelete`](https://www.learnentityframeworkcore.com/dbset/execute-delete) is now available directly in **Entity Framework Core**.

Both methods are **very similar**, and in most cases, you can use **one or the other**.

The main differences are:

* The syntax
* The SQL generated
* The options available

Because the generated SQL is different, **performance can vary** depending on the query and the database provider.

For example, with **SQL Server**, `ExecuteDelete` tries to delete all rows at once, while `DeleteFromQuery` deletes rows in batches using a `BatchSize = 4000` (default value that can be modified).

Both techniques have their **pros and cons**:

* Deleting millions of rows in a single statement can cause **very bad performance** because of transaction log growth (`ExecuteDelete`).
* Deleting rows in smaller batches can often be **much faster** and safer for large operations (`UpdateFromQuery`).

Honestly, you should try both and use the one you feel **more comfortable** with, or the one that gives you the **best performance** in your scenario.

### Why DeleteFromQuery is faster than SaveChanges, BulkSaveChanges, and BulkDelete?

`DeleteFromQuery` executes a statement directly in SQL such as `DELETE FROM [TableName] WHERE [Key]`. 

Other operations normally require one or multiple database round-trips which makes the performance slower.

## Summary & Next Steps

`DeleteFromQuery` lets you delete rows **directly from LINQ**, without loading entities or relying on change tracking.

Because the delete is executed as a **set-based SQL operation**, it scales well and avoids unnecessary memory usage.

This method is **FREE to use** and gives you fine control over how deletes are executed, including batching and throttling for large operations.

If you need to:

* **Insert rows from a query**: Use [InsertFromQuery](/insert-from-query)
* **Update rows from a query**: Use [UpdateFromQuery](/update-from-query)
* **Delete rows from a query**: Use [DeleteFromQuery](/delete-from-query)

Together, these methods allow you to **INSERT, UPDATE, and DELETE directly from LINQ**, while keeping execution fully at the database level.