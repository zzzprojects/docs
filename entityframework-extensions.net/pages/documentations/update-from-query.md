---
Title: Entity Framework UpdateFromQuery – Update Rows Directly from LINQ
MetaDescription: Understand how Entity Framework UpdateFromQuery updates database rows directly from LINQ, without loading entities, tracking changes, or SaveChanges.
LastMod: 2025-12-23
---

# Entity Framework UpdateFromQuery

## What is UpdateFromQuery

`UpdateFromQuery` is a **FREE** method supported by both EF Core and EF6 (see also [InsertFromQuery](/insert-from-query) and [DeleteFromQuery](/delete-from-query)) that lets you update database rows **directly in SQL**:

* Without **reading data** or loading entities into memory
* Without using the **Change Tracker**
* Without calling `SaveChanges`

Think of `UpdateFromQuery` as a SQL UPDATE statement generated from LINQ.
 
This makes updates **dramatically faster** and is ideal for **bulk or set-based operations**, where all rows follow the same rule.

You should use `UpdateFromQuery` when **the update values are the same for all rows**. If the **values come from each entity**, you should instead use [BulkUpdate](/bulk-update), which is designed for entity-based updates.

`UpdateFromQuery` is available in both synchronous and asynchronous versions:

* `UpdateFromQuery`
* `UpdateFromQueryAsync`

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

// UPDATE all customers that are inactive for more than two years
var date = DateTime.Now.AddYears(-2);
context.Customers
    .Where(x => x.IsActive && x.LastLogin < date)
    .UpdateFromQuery(x => new Customer { IsActive = false });

// UPDATE customers by id
context.Customers
    .Where(x => x.ID == userId)
    .UpdateFromQuery(x => new Customer { IsActive = false });
```

[Online Example (EF Core)](https://dotnetfiddle.net/ASfpQe) | [Online Example (EF6)](https://dotnetfiddle.net/9hAsuQ)

In short, it tells the database: **“Update all matching rows like this.”**

## UpdateFromQuery BatchUpdateManager

The `BatchUpdateManager` lets you configure **global options** for `UpdateFromQuery`.

All options defined here are applied **automatically to every `UpdateFromQuery` call**, across your entire application.

You can use it when you want **consistent behavior everywhere**, without repeating configuration on each update.

Because these options are global, any change will affect **all future `UpdateFromQuery` executions**.

The `BatchUpdateManager` has 3 options:
- [BatchUpdateBuilder](#batchupdatebuilder)
- [InMemoryDbContextFactory](#inmemorydbcontextfactory)
- [Hook](#hook)

### BatchUpdateBuilder

The `BatchUpdateBuilder` is an action that lets you set options that will be applied to **every update** executed with the `UpdateFromQuery` method.

This configuration is global and affects **all `UpdateFromQuery` calls**, regardless of the context or query.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

BatchUpdateManager.BatchUpdateBuilder = builder =>
{
    builder.BatchSize = 1000;
};
```

You can use this when you want to enforce the same batch configuration everywhere and avoid repeating options in each call.

### InMemoryDbContextFactory

If you use an **InMemory** provider, you must provide the in-memory context where the logic will be applied.

This configuration is global and will be used by **all `UpdateFromQuery` calls** that run against an in-memory provider.

This is required because we apply some internal logic that could affect your original context. For example, we may need to attach entities in order to update them.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

BatchUpdateManager.InMemoryDbContextFactory = () => new MyMemoryContext(options);
```

You only need to configure this once for the entire application.

### Hook

The hook lets you define **properties that will always be updated to specific values** for all `UpdateFromQuery` calls of the same entity type.

This logic is global and runs **automatically** every time `UpdateFromQuery` is executed for that entity, even if those properties are not specified in the update expression.

You can use it when you want to **force some columns to always be updated**, such as audit fields or system-controlled values.

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

Because hooks are global, the properties defined in the hook will be **applied to every update** for that entity type.

Make sure those values make sense for **all `UpdateFromQuery` operations** on that entity.

## UpdateFromQuery Options

You can also set options **directly inside an `UpdateFromQuery` call**.

These options apply **only to the current execution**, unlike `BatchUpdateManager`, which configures global behavior.

We support the following options:

* [BatchSize](#batchsize)
* [CommandExecuteAction](#commandexecuteaction)
* [Executing](#executing)
* [IgnoreInMemoryAsNoTracking](#ignoreinmemoryasnotracking)
* [UseTableLock](#usetablelock)
* [UseRowLock](#userowlock)

### BatchSize

Allows you to specify **how many rows are updated at a time**.

This option is supported **only for SQL Server**.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

context.Customers
    .Where(x => x.ID == userId)
    .UpdateFromQuery(
        x => new Customer { IsActive = false },
        options => options.BatchSize = 1000
    );
```

You can use this option to reduce lock duration when updating a very large number of rows.

### CommandExecuteAction

Allows you to override **how the command is executed**.

For example, you can log the command text before execution, execute it manually, or wrap it in custom logic.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

context.Customers
    .Where(x => x.ID == userId)
    .UpdateFromQuery(
        x => new Customer { IsActive = false },
        options => options.CommandExecuteAction = (command, batch) =>
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
    .Where(x => x.ID == userId)
    .UpdateFromQuery(
        x => new Customer { IsActive = false },
        options => options.Executing = command =>
        {
            command.CommandText =
                command.CommandText.Replace("[MyTableName]", "[AnotherTableName]");
        }
    );
```

### IgnoreInMemoryAsNoTracking

When enabled, the method will use `AsNoTracking` when materializing the list of entities to update.

This option is useful if you do not want entities to be loaded or tracked in your original context.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

context.Customers
    .Where(x => x.ID == userId)
    .UpdateFromQuery(
        x => new Customer { IsActive = false },
        options => options.IgnoreInMemoryAsNoTracking = true
    );
```

### UseTableLock

When enabled, the query is executed with a **table lock hint**.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

context.Customers
    .Where(x => x.ID == userId)
    .UpdateFromQuery(
        x => new Customer { IsActive = false },
        options => options.UseTableLock = true
    );
```

### UseRowLock

When enabled, the query is executed with a **row lock hint**.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

context.Customers
    .Where(x => x.ID == userId)
    .UpdateFromQuery(
        x => new Customer { IsActive = false },
        options => options.UseRowLock = true
    );
```

## FAQ

### What is the difference with EF Core ExecuteUpdate?

For people using **EF Core 7+**, a similar built-in method called [`ExecuteUpdate`](https://www.learnentityframeworkcore.com/dbset/execute-update) is now available directly in **Entity Framework Core**.

Both methods are **very similar**, and in most cases, you can use **one or the other**.

The main differences are:

* The syntax
* The SQL generated
* The options available

Because the generated SQL is different, **performance can vary** depending on the query and the database provider.

Honestly, try both and use the one you feel **more comfortable** with, or the one that gives you the **best performance** in your scenario.

### Why UpdateFromQuery is faster than SaveChanges, BulkSaveChanges, and BulkUpdate?

`UpdateFromQuery` executes a statement directly in SQL such as `UPDATE [TableName] SET [SetColumnsAndValues] WHERE [Key]`. 

Other operations normally require one or multiple database round-trips which makes the performance slower.

## Summary & Next Steps

`UpdateFromQuery` is the right choice when you want to update many rows **fast**, **directly in SQL**, and **without loading entities**.

This method is **FREE to use** and does not require a license.

Once you are comfortable with it, you will often notice that the same approach works well for other database operations.

If you need to:

- **Insert rows from a query**, without loading entities: Use [InsertFromQuery](/insert-from-query)
- **Update rows from a query**, without loading entities: Use [UpdateFromQuery](/update-from-query)
- **Delete rows from a query**, without loading entities: Use [DeleteFromQuery](/delete-from-query)

Together, `InsertFromQuery`, `UpdateFromQuery`, and `DeleteFromQuery` give you a complete set of **set-based operations** that run directly at the database level.

If your goal is **performance, simplicity, and control**, these methods should become part of your daily Entity Framework toolbox.
