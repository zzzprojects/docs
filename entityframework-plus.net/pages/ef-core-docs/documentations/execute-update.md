---
Title: EF Core ExecuteUpdate: Update Multiple Properties and Future Action
MetaDescription: Discover additional features for EF Core ExecuteUpdate, including setting multiple properties at once, mixing SetProperty with SetProperties, and executing updates later with Future Action.
LastMod: 2026-07-14
---

# EF Core ExecuteUpdate

EF Core 7 introduced [ExecuteUpdate](https://www.learnentityframeworkcore.com/saving/execute-update), a built-in method that performs an update directly in the database without loading entities into memory.

This method covers most scenarios that previously required our [UpdateFromQuery](https://entityframework-extensions.net/update-from-query) method, which our company supported for many years. Today, we generally recommend using the built-in `ExecuteUpdate` method whenever possible.

However, while `ExecuteUpdate` works well, setting several properties individually with `SetProperty` can become repetitive. For this reason, our library provides a `SetProperties` extension that lets you set multiple properties at once by using an object initializer.

You can also mix `SetProperties` with the built-in `SetProperty` method in the same update operation:

* [Basic ExecuteUpdate](#basic-executeupdate) - Update rows directly in the database without loading entities into memory.
* [Using ExecuteUpdate with Multiple Properties](#using-executeupdate-with-multiple-properties) - Set multiple properties at once by using an object initializer.
* [Mixing SetProperty and SetProperties](#mixing-setproperty-and-setproperties) - Combine individual property updates with multiple property updates.
* [Using ExecuteUpdate with Future Action](#using-executeupdate-with-future-action) - Register update operations and execute them later in a centralized location.
* [Using ExecuteUpdate with Hint](#using-executeupdate-with-hint) - Add SQL query hints when you need more control over how the database executes the update operation.

## ExecuteUpdate Examples

### Basic ExecuteUpdate

The `ExecuteUpdate` method updates all rows that match the query directly in the database without loading entities into memory.

Each property must normally be configured by calling `SetProperty`:

```csharp
var rowsAffected = context.Customers
    .Where(x => !x.IsActive)
    .ExecuteUpdate(update => update
        .SetProperty(x => x.Status, "Inactive")
        .SetProperty(x => x.LastUpdatedDate, DateTime.UtcNow));
```

The method returns the number of rows affected by the operation.

Benefits:

* Included directly in EF Core 7 and later
* Updates rows without loading entities into memory
* Executes a single SQL statement
* Returns the number of rows affected
* Supports values calculated from the current database values

This article focuses on additional features built on top of `ExecuteUpdate`. For complete documentation about the built-in EF Core method, see [ExecuteUpdate](https://www.learnentityframeworkcore.com/saving/execute-update).

### Using ExecuteUpdate with Multiple Properties

> **Note:** This feature requires **EF Core 10 or later**. Earlier versions of EF Core do not support extending `ExecuteUpdate` with custom methods such as `SetProperties`.

When several properties must be updated, calling `SetProperty` for every property can become repetitive.

Our library provides the `SetProperties` method, which lets you set multiple properties at once by using an object initializer. This syntax is similar to the syntax supported by `UpdateFromQuery`.

```csharp
// @nuget: Z.EntityFramework.Plus.EFCore
using Z.EntityFramework.Plus;

var rowsAffected = context.Customers
    .Where(x => !x.IsActive)
    .ExecuteUpdate(update =>
    {
        update.SetProperties(x => new Customer
        {
            Status = "Inactive",
            IsArchived = true,
            LastUpdatedDate = DateTime.UtcNow
        });
    });
```

Under the hood, `SetProperties` converts every assigned property into a `SetProperty` call before EF Core executes the update.

This means the operation still uses the built-in `ExecuteUpdate` pipeline and generates a single SQL update statement.

You can also calculate a new value from the current database value:

```csharp
var rowsAffected = context.Customers
    .Where(x => x.IsActive)
    .ExecuteUpdate(update =>
    {
        update.SetProperties(x => new Customer
        {
            UpdateCount = x.UpdateCount + 1,
            FullName = x.FirstName + " " + x.LastName,
            LastUpdatedDate = DateTime.UtcNow
        });
    });
```

The parameter used in the expression represents the current row. You can use it to create values based on existing property values.

Benefits:

* Sets multiple properties at once
* Uses a familiar object initializer syntax
* Reduces repetitive `SetProperty` calls
* Supports constant values
* Supports expressions based on current database values
* Generates a single SQL update statement
* Works with `ExecuteUpdate` and `ExecuteUpdateAsync`

### Mixing SetProperty and SetProperties

You can mix the built-in `SetProperty` method with our `SetProperties` method in the same `ExecuteUpdate` operation.

This is useful when some properties are easier to configure individually while other properties are more readable inside an object initializer.

```csharp
// @nuget: Z.EntityFramework.Plus.EFCore
using Z.EntityFramework.Plus;

var rowsAffected = context.EntitySimples
    .ExecuteUpdate(update =>
    {
        update.SetProperty(
            x => x.ColumnInt,
            x => x.ColumnInt + 10);

        update.SetProperties(x => new EntitySimple
        {
            ColumnString = "a",
            ColumnString2 = x.ColumnString2 + "zzz"
        });
    });
```

In this example:

* `ColumnInt` is increased by `10` with `SetProperty`
* `ColumnString` is changed to a constant value with `SetProperties`
* `ColumnString2` is updated from its current database value with `SetProperties`

All property updates are combined into the same SQL update statement.

You can call `SetProperty` and `SetProperties` multiple times when needed:

```csharp
var rowsAffected = context.Customers
    .Where(x => x.IsActive)
    .ExecuteUpdate(update =>
    {
        update.SetProperty(
            x => x.LoginCount,
            x => x.LoginCount + 1);

        update.SetProperties(x => new Customer
        {
            Status = "Processed",
            LastUpdatedDate = DateTime.UtcNow
        });

        update.SetProperty(
            x => x.Version,
            x => x.Version + 1);
    });
```

Benefits:

* Combines `SetProperty` and `SetProperties`
* Lets you choose the clearest syntax for every property
* Supports multiple calls in the same operation
* Combines all updates into a single SQL statement
* Keeps compatibility with the built-in EF Core API

### Using ExecuteUpdate with Future Action

You can combine `ExecuteUpdate` with [Future Action](https://entityframework-plus.net/future-action) when you want to register one or more update operations and execute them later in a single place.

This is especially useful when your `ExecuteUpdate` calls are located in multiple repositories. Each repository can register its own update operation without executing it immediately, and your application can decide later when all registered actions should be executed.

```csharp
// @nuget: Z.EntityFramework.Plus.EFCore
using Z.EntityFramework.Plus;

context.FutureAction(x =>
    x.Customers
        .Where(c => !c.IsActive)
        .ExecuteUpdate(update =>
        {
            update.SetProperties(c => new Customer
            {
                Status = "Inactive",
                IsArchived = true
            });
        }));

context.FutureAction(x =>
    x.Orders
        .Where(o => o.IsExpired)
        .ExecuteUpdate(update =>
        {
            update.SetProperty(
                o => o.Status,
                "Expired");
        }));

// Execute all registered actions
context.ExecuteFutureAction();
```

When `ExecuteFutureAction()` is called, all registered actions are executed in the order they were added to the queue.

Benefits:

* Defers execution until you decide to execute it
* Centralizes execution when update operations are registered from multiple repositories
* Keeps repository logic separated while keeping execution controlled in one place
* Can execute multiple update operations together
* Supports `SetProperty` and `SetProperties`
* Supports transactional execution with `ExecuteFutureAction(true)` or `ExecuteFutureActionAsync(true)`

### Using ExecuteUpdate with Hint

Sometimes you need to control how the database executes an update operation. For example, you might want to apply SQL query hints to influence locking behavior or improve performance for a specific scenario.

Our library lets you apply query hints to `ExecuteUpdate` by using the [WithHint](https://entityframework-plus.net/ef-core-query-hint) extension method before calling `ExecuteUpdate` or `ExecuteUpdateAsync`.

```csharp
// @nuget: Z.EntityFramework.Plus.EFCore
using Z.EntityFramework.Plus;

var rowsAffected = context.Customers
    .Where(x => !x.IsActive)
    .WithHint(SqlServerTableHintFlags.TABLOCK)
    .ExecuteUpdate(update =>
    {
        update.SetProperties(x => new Customer
        {
            Status = "Inactive",
            IsArchived = true
        });
    });
```

Query hints are provider-specific and are only applied when supported by the current database provider.

Benefits:

* Applies SQL query hints to `ExecuteUpdate`
* Helps control locking behavior for update operations
* Can improve performance in specific scenarios
* Supports `SetProperty` and `SetProperties`
* Works with `ExecuteUpdate` and `ExecuteUpdateAsync`

## Summary

In this article, you learned how to use EF Core's `ExecuteUpdate` method together with additional features provided by Entity Framework Plus.

While we generally recommend using the built-in `ExecuteUpdate` method, our library offers extensions for scenarios where you want a simpler syntax or more control:

* Use `SetProperties` to update multiple properties at once with an object initializer.
* Mix `SetProperty` and `SetProperties` in the same operation when each syntax is useful for different properties.
* Use expressions inside `SetProperties` to calculate values from the current database values.
* Use `FutureAction` to register one or more update operations and execute them later in a centralized location.
* Use `WithHint` to apply provider-specific query hints to the generated update command.

These features build on top of `ExecuteUpdate` while keeping the built-in EF Core behavior and execution pipeline.
