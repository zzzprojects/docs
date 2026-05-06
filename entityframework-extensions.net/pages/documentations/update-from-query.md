---
Title: UpdateFromQuery – Update Rows Directly from LINQ in Entity Framework 
MetaDescription: Understand how Entity Framework UpdateFromQuery updates database rows directly from LINQ, without loading entities, tracking changes, or SaveChanges.
LastMod: 2026-05-05
---

# What is UpdateFromQuery

`UpdateFromQuery` is a **FREE** method supported by both **EF Core** and **EF6**. It lets you update database rows **directly in SQL**:

* Without **reading data** or loading entities into memory
* Without using the **Change Tracker**
* Without calling `SaveChanges`

Think of `UpdateFromQuery` as a **SQL UPDATE statement generated from LINQ**.
 
`UpdateFromQuery` is a set-based operation executed entirely in SQL.
 
This makes updates **dramatically faster** and is ideal for **bulk or set-based operations**, where all rows follow the same rule.

You should use `UpdateFromQuery` when **the update values are the same for all rows**. If the **values come from each entity**, you should instead use [BulkUpdate](/bulk-update), which is designed for entity-based updates.

## UpdateFromQuery Example

### Update all rows in a table

You can update all rows in a table by calling `UpdateFromQuery` **without a filter**.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

// UPDATE all customers
await context.Customers.UpdateFromQueryAsync(x => new Customer { IsActive = false, LastModifiedDate = DateTime.Now });
```

[Online Example](https://dotnetfiddle.net/eSBx9O)

This will update **all rows in the table**, so use it carefully.

### Update rows using a "Where" filter

Like a standard LINQ query, you can filter the rows you want to update using the `Where` method.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

// UPDATE all customers that are inactive for more than two years
var date = DateTime.Now.AddYears(-2);
await context.Customers
    .Where(x => x.IsActive && x.LastLogin < date)
    .UpdateFromQueryAsync(x => new Customer { IsActive = false, LastModifiedDate = DateTime.Now });

// UPDATE customers by id
await context.Customers
    .Where(x => x.ID == userId)
    .UpdateFromQueryAsync(x => new Customer { IsActive = false, LastModifiedDate = DateTime.Now });
```

[Online Example](https://dotnetfiddle.net/uoqZCN)

In short, it tells the database: **“UPDATE all rows that match your filter.”**

### Update rows using a "WhereBulkContains" filter

You can also use methods like [WhereBulkContains](/where-bulk-contains) to filter rows to update, especially when working with large lists of values.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

var ids = new List<int>() { 1, 2 };

// UPDATE customers by id
await context.Customers
    .WhereBulkContains(ids)
    .UpdateFromQueryAsync(x => new Customer { IsActive = false, LastModifiedDate = DateTime.Now });
```

[Online Example](https://dotnetfiddle.net/opah3n)

### Update rows using existing row values

You can use existing database values when updating rows.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

await context.Customers
    .Where(x => x.Counter >= 1)
    .UpdateFromQueryAsync(x => new Customer 
    {
        Counter = x.Counter + 10
    });
```

[Online Example](https://dotnetfiddle.net/ASfpQe)

In this example, the current value of `Counter` is incremented directly in SQL.

### Update rows using options

You can also use the `UpdateFromQuery` method by passing [options](#updatefromquery-options) to customize how the update is executed.

For example, you can control **batching, locking, or execution behavior**.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

await context.Customers
    .Where(x => !x.IsActive)
    .UpdateFromQueryAsync(
        x => new Customer { IsActive = false, LastModifiedDate = DateTime.Now },
        options => options.BatchSize = 1000);
```

[Online Example](https://dotnetfiddle.net/2RnaXR)

## UpdateFromQuery BatchUpdateManager

The `BatchUpdateManager` lets you configure **global options** for `UpdateFromQuery`.

All options defined here are applied **automatically to every `UpdateFromQuery` call** across your entire application.

Use it when you want **consistent behavior everywhere**, without repeating configuration on each update.

If you need different behavior per query, use [options](#updatefromquery-options) instead.

Because these options are global, any change will affect **all future `UpdateFromQuery` executions**.

The `BatchUpdateManager` supports the following options:

* [BatchUpdateBuilder](#batchupdatebuilder)
* [InMemoryDbContextFactory](#inmemorydbcontextfactory)
* [Hook](#hook)

### BatchUpdateBuilder

The `BatchUpdateBuilder` is an action that lets you configure options applied to **every `UpdateFromQuery` execution**.

This configuration is global and affects **all `UpdateFromQuery` calls**, regardless of the context or query.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

BatchUpdateManager.BatchUpdateBuilder = builder =>
{
    builder.BatchSize = 1000;
};
```

Use this option when you want to **enforce consistent behavior across your application** and avoid repeating configuration in each call.

### InMemoryDbContextFactory

If you use the **EF Core InMemory provider**, you must provide the in-memory context where the operation will be executed.

This configuration is global and will be used by **all `UpdateFromQuery` calls** running against the InMemory provider.

This is required because some internal operations may affect your original context. For example, entities may need to be attached in order to be updated.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

BatchUpdateManager.InMemoryDbContextFactory = () => new MyMemoryContext(options);
```

You only need to configure this **once** for the entire application.

### Hook

The hook lets you define **properties that are always updated to specific values** for all `UpdateFromQuery` calls of the same entity type.

This logic is global and runs **automatically** every time `UpdateFromQuery` is executed for that entity, even if those properties are not specified in the update expression.

You can use it to **enforce updates on specific columns**, such as audit fields or system-controlled values.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

BatchUpdateManager.Hook<Customer>(() => new
{
    ModifiedBy = LoggedUser.Name,
    ModifiedDate = DateTime.Now
});

using (var context = new ModelAndContext.EntityContext())
{
    // ColumInt will be updated to 50
    // ModifiedBy and ModifiedDate will also be updated automatically
    context.Customers.UpdateFromQuery(x => new Customer { ColumInt = 50 });
}
```

Because hooks are global, the properties defined in the hook are **applied to every update** for that entity type.

Make sure these values make sense for **all `UpdateFromQuery` operations** on that entity.

## UpdateFromQuery Options

You can also set options **directly inside an `UpdateFromQuery` call**.

These options apply **only to the current execution**, unlike [BatchUpdateManager](#updatefromquery-batchupdatemanager), which configures global behavior.

Use these options to control **performance and execution behavior**.

The following options are supported:

* [BatchSize](#batchsize)
* [CommandExecuteAction](#commandexecuteaction)
* [Executing](#executing)
* [IgnoreInMemoryAsNoTracking](#ignoreinmemoryasnotracking)
* [UseTableLock](#usetablelock)
* [UseRowLock](#userowlock)

### BatchSize

Allows you to specify **how many rows are updated per batch**.

This option is supported **only for SQL Server**.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

context.Customers
    .Where(x => x.IsActive)
    .UpdateFromQuery(
        x => new Customer { IsActive = false },
        options => options.BatchSize = 1000
    );
```

You can use this option to **reduce lock duration** when updating a very large number of rows.

### CommandExecuteAction

Allows you to override **how the command is executed**.

For example, you can **log the command text**, execute it manually, or wrap it with your own custom logic.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

context.Customers
    .Where(x => x.IsActive)
    .UpdateFromQuery(
        x => new Customer { IsActive = false },
        options => options.CommandExecuteAction = (command, batch) =>
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

It is useful when you want to **inspect, modify, or log the command**, such as changing table names or adding hints dynamically.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

context.Customers
    .Where(x => x.IsActive)
    .UpdateFromQuery(
        x => new Customer { IsActive = false },
        options => options.Executing = command =>
        {
            // Modify the generated SQL before execution
            command.CommandText =
                command.CommandText.Replace("[MyTableName]", "[AnotherTableName]");
        }
    );
```

Use this option when you need **last-minute control over the generated SQL**.

Use it carefully, as modifying SQL can lead to **unexpected behavior**.

### IgnoreInMemoryAsNoTracking

This option is used **only with the EF Core InMemory provider**.

By default, entities are materialized using `AsNoTracking` to **avoid tracking overhead**.

When `IgnoreInMemoryAsNoTracking` is set to `true`, the method **does not use `AsNoTracking`**, and entities will be **tracked by the context** instead.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

context.Customers
    .Where(x => x.IsActive)
    .UpdateFromQuery(
        x => new Customer { IsActive = false },
        options => options.IgnoreInMemoryAsNoTracking = true
    );
```

Use this option when working with the InMemory provider and you need entities to be **tracked instead of ignored**.

This can be useful if you have already loaded some entities and want to see the changes reflected in those instances, since they are tracked by the same context.

### UseTableLock

When enabled, the query is executed with a **table lock hint**.

This means the database will lock the **entire table** during the operation.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

context.Customers
    .Where(x => x.IsActive)
    .UpdateFromQuery(
        x => new Customer { IsActive = false },
        options => options.UseTableLock = true
    );
```

Use this option to **reduce lock overhead** and improve performance for large operations, especially when no other queries need to access the table at the same time.

This may **block other queries** while the operation is running.

### UseRowLock

When enabled, the query is executed with a **row lock hint**.

This means the database will lock **individual rows** instead of the entire table during the operation.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

context.Customers
    .Where(x => x.IsActive)
    .UpdateFromQuery(
        x => new Customer { IsActive = false },
        options => options.UseRowLock = true
    );
```

Use this option to **reduce blocking** and allow other queries to access the table while the operation is running.

## FAQ

### How to increase the CommandTimeout?

If a timeout occurs, you can increase the command timeout by setting it on the database context **before calling** the `UpdateFromQuery` method.

This is useful when updating a large number of rows or when the query takes more time to execute.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

context.Database.SetCommandTimeout(TimeSpan.FromSeconds(60));

context.Customers
    .Where(x => x.ID == userId)
    .UpdateFromQuery(x => new Customer { IsActive = false });
```

### Is the UpdateFromQuery feature FREE?

Yes, you don’t need to buy a commercial license to use it.

`UpdateFromQuery` is **completely free**, including for **commercial use**.

You can use it in both personal and professional projects without any restriction.

### What is the difference with EF Core ExecuteUpdate?

For users on **EF Core 7+**, a built-in method called [`ExecuteUpdate`](https://www.learnentityframeworkcore.com/saving/execute-update) is available directly in **Entity Framework Core**.

Both methods are **very similar**, and in most cases, you can use **either one**.

The main differences are:

* The **syntax**
* The **SQL generated**
* The **options and flexibility available**

Because the generated SQL is different, **performance can vary** depending on the query and the database provider.

Each approach has its **strengths**:

* **ExecuteUpdate:** Often generates more optimized SQL
* **UpdateFromQuery:** Usually offers a simpler and more flexible syntax

In practice, the best choice depends on your scenario. Try both and use the one that gives you the **best performance** or the **simplest implementation** for your needs.

### Why is UpdateFromQuery faster than SaveChanges, BulkSaveChanges, and BulkUpdate?

`UpdateFromQuery` executes a statement directly in SQL, such as `UPDATE [TableName] SET ... WHERE ...`.

This means everything is done **on the database side**, without loading entities into memory.

Other operations usually:

* Load data into memory
* Track entities
* Perform one or multiple database round-trips

Because `UpdateFromQuery` skips all those steps, it is usually **much faster and more efficient**, especially for large operations.

## Summary

`UpdateFromQuery` is the right choice when you want to update many rows **fast**, **directly in SQL**, and **without loading entities**.

This method is **completely free**, including for commercial use.

Once you start using it, you’ll often find that the same approach works well for other operations.

If you need to:

* **Insert rows from a query**: Use [InsertFromQuery](/insert-from-query)
* **Update rows from a query**: Use [UpdateFromQuery](/update-from-query)
* **Delete rows from a query**: Use [DeleteFromQuery](/delete-from-query)

Together, `InsertFromQuery`, `UpdateFromQuery`, and `DeleteFromQuery` provide a complete set of **set-based operations executed directly in SQL**.

If your goal is **performance, simplicity, and control at scale**, these methods should become part of your daily Entity Framework toolbox.
