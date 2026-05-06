---
Title: DeleteFromQuery – Delete Rows Directly from LINQ in Entity Framework 
MetaDescription: Understand how Entity Framework DeleteFromQuery deletes database rows directly from LINQ, without loading entities, tracking changes, or SaveChanges.
LastMod: 2026-05-05
---

# What is DeleteFromQuery

`DeleteFromQuery` is a **FREE** method supported by both **EF Core** and **EF6**. It lets you delete database rows **directly in SQL**:

* Without **reading data** or loading entities into memory
* Without using the **Change Tracker**
* Without calling `SaveChanges`

Think of `DeleteFromQuery` as a **SQL DELETE statement generated from LINQ**.

`DeleteFromQuery` is a set-based operation executed entirely in SQL.

You should use `DeleteFromQuery` when you want to **delete rows from a database table that can be represented by a LINQ query**. If you want to delete entities instead, you should use [BulkDelete](/bulk-delete).

## DeleteFromQuery Examples

### Delete all rows in a table

You can delete all rows in a table by calling `DeleteFromQuery` **without a filter**.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

// DELETE all customers
await context.Customers.DeleteFromQueryAsync();
```

[Online Example](https://dotnetfiddle.net/gQHmpW)

This will delete **all rows in the table**, so use it carefully.

### Delete rows using a "Where" filter

Like a standard LINQ query, you can filter the rows you want to delete using the `Where` method.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

// DELETE all customers that are inactive
await context.Customers
    .Where(x => !x.IsActive)
    .DeleteFromQueryAsync();

// DELETE customers by id
await context.Customers
    .Where(x => x.ID == userId)
    .DeleteFromQueryAsync();
```

[Online Example](https://dotnetfiddle.net/nGIOTQ)

In short, it tells the database: **“DELETE all rows that match your filter.”**

### Delete rows using a "WhereBulkContains" filter

You can also use methods like [WhereBulkContains](/where-bulk-contains) to filter rows to delete, especially when working with large lists of values.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

var ids = new List<int>() { 1, 2 };

// DELETE customers by id
await context.Customers
    .WhereBulkContains(ids)
    .DeleteFromQueryAsync();
```

[Online Example](https://dotnetfiddle.net/DctDOM)

### Delete rows using options

You can also use the `DeleteFromQuery` method by passing [options](#deletefromquery-options) to customize how the delete is executed.

For example, you can control **batching, locking, or execution behavior**.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

await context.Customers
    .Where(x => !x.IsActive)
    .DeleteFromQueryAsync(options => options.BatchSize = 1000);
```

[Online Example](https://dotnetfiddle.net/q1zqYR)

## DeleteFromQuery BatchDeleteManager

The `BatchDeleteManager` lets you configure **global options** for `DeleteFromQuery`.

All options defined here are applied **automatically to every `DeleteFromQuery` call** across your entire application.

Use it when you want **consistent behavior everywhere**, without repeating configuration on each delete.

If you need different behavior per query, use [options](#deletefromquery-options) instead.

Because these options are global, any change will affect **all future `DeleteFromQuery` executions**.

The `BatchDeleteManager` supports the following options:

* [BatchDeleteBuilder](#batchdeletebuilder)
* [InMemoryDbContextFactory](#inmemorydbcontextfactory)

### BatchDeleteBuilder

The `BatchDeleteBuilder` is an action that lets you configure options applied to **every `DeleteFromQuery` execution**.

This configuration is global and affects **all `DeleteFromQuery` calls**, regardless of the context or query.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

BatchDeleteManager.BatchDeleteBuilder = builder =>
{
    builder.BatchSize = 1000;
};
```

Use this option when you want to **enforce consistent behavior across your application** and avoid repeating configuration in each call.

### InMemoryDbContextFactory

If you use the **EF Core InMemory provider**, you must provide the in-memory context where the operation will be executed.

This configuration is global and will be used by **all `DeleteFromQuery` calls** running against the InMemory provider.

This is required because some internal operations may affect your original context. For example, entities may need to be attached in order to be removed.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

BatchDeleteManager.InMemoryDbContextFactory = () => new MyMemoryContext(options);
```

You only need to configure this **once** for the entire application.

## DeleteFromQuery Options

You can also set options **directly inside a `DeleteFromQuery` call**.

These options apply **only to the current execution**, unlike [BatchDeleteManager](#deletefromquery-batchdeletemanager), which configures global behavior.

Use these options to control **performance and execution behavior**.

The following options are supported:

* [BatchSize](#batchsize)
* [BatchDelayInterval](#batchdelayinterval)
* [CommandExecuteAction](#commandexecuteaction)
* [Executing](#executing)
* [IgnoreInMemoryAsNoTracking](#ignoreinmemoryasnotracking)
* [InMemoryDbContextFactory](#inmemorydbcontextfactory-1)
* [UseTableLock](#usetablelock)

### BatchSize

Allows you to specify **how many rows are deleted per batch**.

This option is supported **only for SQL Server**.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

context.Customers
    .Where(x => !x.IsActive)
    .DeleteFromQuery(options => options.BatchSize = 1000);
```

You can use this option to **reduce lock duration** when deleting a very large number of rows.

### BatchDelayInterval

Allows you to **wait a specified number of milliseconds between batch deletes** (not before the first batch).

This option **only works with SQL Server** and uses the SQL statement `WAITFOR DELAY '{BatchDelayInterval}'`.

Use this option to **throttle large delete operations**, helping to reduce database pressure, avoid long locks, and limit transaction log growth.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

context.Customers
    .Where(x => !x.IsActive)
    .DeleteFromQuery(options => options.BatchDelayInterval = 2000);
```

This increases the total execution time, but improves **stability and reliability** for large operations.

### CommandExecuteAction

Allows you to override **how the command is executed**.

For example, you can **log the command text**, execute it manually, or wrap it with your own custom logic.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

context.Customers
    .Where(x => !x.IsActive)
    .DeleteFromQuery(
        options => options.CommandExecuteAction = command =>
        {
            Console.WriteLine(command.CommandText);

            // Execute the command manually
            return command.ExecuteNonQuery();
        }
    );
```

Use this option when you need **full control over command execution**, such as logging, debugging, or adding custom behavior.

### Executing

The `Executing` action is called **just before the command is executed**, and **before interceptors run**.

It lets you **inspect, modify, or log the generated SQL** before it runs, for example to change table names or add hints dynamically.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

context.Customers
    .Where(x => !x.IsActive)
    .DeleteFromQuery(
        options => options.Executing = command =>
        {
            // Modify the generated SQL before execution
            command.CommandText =
                command.CommandText.Replace("[MyTableName]", "[AnotherTableName]");
        }
    );
```

Use this option when you need **last-minute control over the SQL**.

Use it carefully, as modifying SQL can lead to **unexpected behavior**.

### IgnoreInMemoryAsNoTracking

This option is used **only with the EF Core InMemory provider**.

By default, entities are materialized using `AsNoTracking` to **avoid tracking overhead**.

When `IgnoreInMemoryAsNoTracking` is set to `true`, the method **does not use `AsNoTracking`**, and entities will be **tracked by the context** instead.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

context.Customers
    .Where(x => !x.IsActive)
    .DeleteFromQuery(options => options.IgnoreInMemoryAsNoTracking = true);
```

Use this option when working with the InMemory provider and you need entities to be **tracked instead of ignored**.

This can be useful if you have already loaded some entities and want to see the changes reflected in those instances, since they are tracked by the same context.

### InMemoryDbContextFactory

This option is used **only with the EF Core InMemory provider**.

It works the same way as the global `BatchDeleteManager.InMemoryDbContextFactory` option, but applies **only to the current `DeleteFromQuery` execution**.

Use this option when you need to provide a custom `DbContext` factory for the InMemory provider.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

context.Customers
    .Where(x => !x.IsActive)
    .DeleteFromQuery(options => 
        options.InMemoryDbContextFactory = () => new MyMemoryContext(options));
```

### UseTableLock

When enabled, the query is executed with a **table lock hint**.

This means the database will lock the **entire table** during the operation.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

context.Customers
    .Where(x => !x.IsActive)
    .DeleteFromQuery(options => options.UseTableLock = true);
```

Use this option to **reduce lock overhead** and improve performance for large operations, especially when no other queries need to access the table at the same time.

This may **block other queries** while the operation is running.

## FAQ

### How to increase the CommandTimeout?

If a timeout occurs, you can increase the command timeout by setting it on the database context **before calling** the `DeleteFromQuery` method.

This is useful when deleting a large number of rows or when the query takes more time to execute.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

context.Database.SetCommandTimeout(TimeSpan.FromSeconds(60));

context.Customers
    .Where(x => x.ID == userId)
    .DeleteFromQuery();
```

### Is the DeleteFromQuery feature FREE?

Yes, you don’t need to buy a commercial license to use it.

`DeleteFromQuery` is **completely free**, including for **commercial use**.

You can use it in both personal and professional projects without any restriction.

### What is the difference with EF Core ExecuteDelete?

For users on **EF Core 7+**, a built-in method called [`ExecuteDelete`](https://www.learnentityframeworkcore.com/saving/execute-delete) is available directly in **Entity Framework Core**.

Both methods are **very similar**, and in most cases, you can use **either one**.

The main differences are:

* The **syntax**
* The **SQL generated**
* The **options and flexibility available**

Because the generated SQL is different, **performance can vary** depending on the query and the database provider.

For example, with **SQL Server**, `ExecuteDelete` usually deletes all rows in a single statement, while `DeleteFromQuery` deletes rows in **batches** (default `BatchSize = 4000`, configurable).

Each approach has its **strengths**:

* **ExecuteDelete:** Simpler and often efficient for smaller or moderate deletes
* **DeleteFromQuery:** Safer and more scalable for very large deletes due to batching

Deleting millions of rows in a single statement can lead to **poor performance** because of transaction log growth, while batching operations can help **reduce pressure** on the database and improve stability.

In practice, the best choice depends on your scenario. Try both and use the one that gives you the **best performance** or the **most control**.

### Why is DeleteFromQuery faster than SaveChanges, BulkSaveChanges, and BulkDelete?

`DeleteFromQuery` executes a statement directly in SQL, such as `DELETE FROM [TableName] WHERE ...`.

This means everything is done **on the database side**, without loading entities into memory.

Other operations usually:

* Load data into memory
* Track entities
* Perform one or multiple database round-trips

Because `DeleteFromQuery` skips all those steps, it is usually **much faster and more efficient**, especially for large operations.

## Summary

`DeleteFromQuery` lets you delete rows **directly from LINQ**, without loading entities or using change tracking.

Because the delete is executed as a **set-based SQL operation**, it scales very well and keeps memory usage low.

This method is **completely free**, including for commercial use, and gives you full control over how deletes are executed, including batching and throttling for large operations.

If you need to:

- **Insert rows from a query**: Use [InsertFromQuery](/insert-from-query)  
- **Update rows from a query**: Use [UpdateFromQuery](/update-from-query)  
- **Delete rows from a query**: Use [DeleteFromQuery](/delete-from-query)  

Together, these methods provide a complete set of **set-based operations executed directly in SQL**.
