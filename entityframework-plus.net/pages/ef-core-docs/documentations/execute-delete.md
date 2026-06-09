---
Title: EF Core ExecuteDelete: Batch Deletes and Future Action
MetaDescription: Discover additional features for EF Core ExecuteDelete, including batch deletes and Future Action. Delete large datasets more efficiently and execute delete operations later in a centralized location.
LastMod: 2026-06-09
---

# EF Core ExecuteDelete

EF Core 7 introduced [ExecuteDelete](https://www.learnentityframeworkcore.com/saving/execute-delete), a built-in method that performs a delete directly in the database without loading entities into memory.

This method covers most scenarios that previously required our [DeleteFromQuery](https://entityframework-extensions.net/delete-from-query) method, which our company supported for many years. Today, we generally recommend using the built-in `ExecuteDelete` method whenever possible.

However, while `ExecuteDelete` covers most scenarios, some real-world situations can still benefit from additional features. For this reason, our library provides extensions that build on top of `ExecuteDelete`:

- [Basic ExecuteDelete](#basic-executedelete) - Delete rows directly in the database without loading entities into memory.
- [Using ExecuteDelete with Batch](#using-executedelete-with-batch) - Delete rows in batches to better control transaction log growth and large delete operations.
- [Using ExecuteDelete with Future Action](#using-executedelete-with-future-action) - Register delete operations and execute them later in a centralized location.

## ExecuteDelete Example

### Basic ExecuteDelete

The `ExecuteDelete` method deletes all rows that match the query directly in the database without loading entities into memory.

```csharp
var rowsAffected = context.Customers
    .Where(x => x.IsDeleted)
    .ExecuteDelete();
````

The method returns the number of rows affected by the operation.

Benefits:

* Included directly in EF Core 7 and later
* Deletes rows without loading entities into memory
* Executes a single SQL statement
* Returns the number of rows affected

This article focuses on additional features built on top of `ExecuteDelete`. For complete documentation about the built-in EF Core method, see [ExecuteDelete](https://www.learnentityframeworkcore.com/saving/execute-delete).

### Using ExecuteDelete with Batch

A major limitation of `ExecuteDelete` is that it deletes all matching rows in a single operation. While this works well for smaller datasets, it can become problematic when deleting millions of rows. In such cases, the transaction log can grow very quickly, increasing I/O, extending lock durations, and significantly impacting performance.

To solve this issue, our library provides `ExecuteDeleteBatch` and `ExecuteDeleteBatchAsync`. These methods take a batch size as a parameter and repeatedly execute `ExecuteDelete` until no more rows match the query. The method returns the total number of rows deleted across all batches.

```csharp
// @nuget: Z.EntityFramework.Plus.EFCore
using Z.EntityFramework.Plus;

var rowsAffected = context.Customers
    .Where(x => x.IsDeleted)
    .ExecuteDeleteBatch(25000);
````

[Online Example](https://dotnetfiddle.net/H79INH)

By processing rows in smaller batches, you can better control transaction log growth and avoid the performance issues often associated with very large delete operations.

Benefits:

* Deletes rows in batches instead of all at once
* Returns the total number of rows deleted across all batches
* Helps control transaction log growth
* Reduces the risk of long-running delete operations
* Automatically participates in the current transaction when one is already in progress
* Otherwise, executes all batches within its own transaction to ensure consistency

### Using ExecuteDelete with Future Action

You can combine `ExecuteDelete` with [Future Action](https://entityframework-plus.net/future-action) when you want to register one or more delete operations and execute them later in a single place.

This is especially useful when your `ExecuteDelete` calls are located in multiple repositories. Each repository can register its own delete operation without executing it immediately, and your application can decide later when all registered actions should be executed.

```csharp
// @nuget: Z.EntityFramework.Plus.EFCore
using Z.EntityFramework.Plus;

context.FutureAction(x =>
    x.Customers
     .Where(c => c.IsDeleted)
     .ExecuteDelete());

context.FutureAction(x =>
    x.Orders
     .Where(o => o.IsArchived)
     .ExecuteDelete());

// Execute all registered actions
context.ExecuteFutureAction();
```

[Online Example](https://dotnetfiddle.net/twk4p0)

When `ExecuteFutureAction()` is called, all registered actions are executed in the order they were added to the queue.

Benefits:

* Defers execution until you decide to execute it
* Centralizes execution when delete operations are registered from multiple repositories
* Keeps repository logic separated while keeping execution controlled in one place
* Can execute multiple delete operations together
* Supports transactional execution with `ExecuteFutureAction(true)` or `ExecuteFutureActionAsync(true)`

## Summary

In this article, you learned how to use EF Core's `ExecuteDelete` method together with additional features provided by Entity Framework Plus.

While we generally recommend using the built-in `ExecuteDelete` method, our library offers extensions for scenarios where you need more control:

- Use `ExecuteDeleteBatch` and `ExecuteDeleteBatchAsync` to delete rows in batches, helping control transaction log growth and improve performance when working with very large tables.
- Use `FutureAction` to register one or more delete operations and execute them later in a centralized location, which is especially useful when working with multiple repositories.

These features build on top of `ExecuteDelete` and help address common challenges encountered in large applications and production environments.