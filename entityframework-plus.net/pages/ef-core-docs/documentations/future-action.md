---
Title: EF Core Future Action: Queue Bulk Operations for Later Execution
MetaDescription: Learn how EF Core Future Action lets you queue bulk inserts, updates, and deletes for centralized execution with Entity Framework Extensions.
LastMod: 2026-05-26
---

# EF Core Future Action with Entity Framework Extensions

Future Action allows you to register multiple actions and execute them later.

This is different from [Query Future](/ef-core-query-future). With Query Future, we try to batch multiple queries together into fewer database round trips when possible. With Future Action, we don’t merge actions together. We simply queue them and execute them later, one after another.

A common example is registering multiple [Bulk Insert](https://entityframework-extensions.net/bulk-insert) operations and executing them later in a single place. This is especially useful when using the repository pattern, where each repository can register its own action while keeping execution centralized.

```csharp
// using Z.EntityFramework.Plus; // Don't forget to include this.
var context = new EntitiesContext();

context.FutureActionAsync(context => context.BulkInsertAsync(customers));
context.FutureActionAsync(context => context.BulkInsertAsync(invoices,
    options => options.IncludeGraph = true));

// true = execute within a transaction
await context.ExecuteFutureActionAsync(true);
```

> ⚠️ **Warning**
> `FutureActionAsync` is not yet released (as of 2026-06-09), but the related documentation is already available.
>
> If you use `FutureActionAsync`, you must use `ExecuteFutureActionAsync`.
>
> Mixing synchronous and asynchronous Future Action methods will throw an exception to prevent unexpected behavior.

## Available Methods

To register an action:

* `FutureAction(action)`
* `FutureActionAsync(action)`

To execute registered actions:

* `ExecuteFutureAction()`
* `ExecuteFutureAction(useTransaction)`
* `ExecuteFutureAction(executeFutureActionOption)`
* `ExecuteFutureActionAsync(useTransaction)`
* `ExecuteFutureActionAsync(cancellationToken)`
* `ExecuteFutureActionAsync(useTransaction, cancellationToken)`
* `ExecuteFutureActionAsync(executeFutureActionOption)`
* `ExecuteFutureActionAsync(executeFutureActionOption, cancellationToken)`

## Future Action Example

### Execute Multiple Operations at Once

One of the main reasons to use `FutureAction` is to register multiple operations and execute them later in a single place.

For example, instead of executing each operation immediately, you can queue them first and execute everything when ready.

```csharp
context.FutureAction(x =>
    x.Users.Where(u => !u.IsActive).ExecuteDelete());

context.FutureAction(x =>
    x.Orders.Where(o => o.Total == 0).ExecuteDelete());

context.FutureAction(x =>
    x.Customers.Where(c => c.IsArchived).ExecuteDelete());

context.ExecuteFutureAction();
```

This is especially useful when actions are registered from different parts of your application, but you want to control when they are executed.

### Using FutureAction with the Repository Pattern

`FutureAction` works very well with the repository pattern, where each repository can register its own operation without executing it immediately.

This lets you keep repository logic separated while still executing everything later in a single place.

```csharp
customerRepository.QueueDeleteInactiveCustomers();
orderRepository.QueueArchiveCompletedOrders();
logRepository.QueueDeleteOldLogs();

context.ExecuteFutureAction();
```

In this example, each repository registers its own action, and the final execution happens only when `ExecuteFutureAction()` is called.

### Conditionally Queue Operations

You can also register actions conditionally and only execute the operations that are needed.

This is useful when the actions to perform depend on runtime conditions or user choices.

```csharp
if (deleteInactiveUsers)
{
    context.FutureAction(x =>
        x.Users
         .Where(u => !u.IsActive)
         .Delete());
}

if (archiveCompletedOrders)
{
    context.FutureAction(x =>
        x.Orders
         .Where(o => o.Status == OrderStatus.Completed)
         .Update(o => new Order
         {
             IsArchived = true
         }));
}

context.ExecuteFutureAction();
```

Only the actions that were registered will be executed when `ExecuteFutureAction()` is called.

### Queue Different Operation Types

`FutureAction` is not limited to a single type of operation. You can register different operations and execute them together later.

For example, you can mix batch updates, batch deletes, and other supported operations in the same execution queue.

```csharp
context.FutureAction(x =>
    x.Users.Where(u => !u.IsInactive)
     .ExecuteUpdate(s => s.SetProperty(u => u.LastLoginReminderSent, DateTime.UtcNow)));

context.FutureAction(x =>
    x.Sessions.Where(s => s.IsExpired).ExecuteDelete());

context.FutureAction(x =>
    x.AuditLogs.Where(a => a.CreatedDate < DateTime.UtcNow.AddYears(-1)).ExecuteDelete());

context.ExecuteFutureAction();
```

This gives you flexibility to centralize different database operations and execute them when needed.

## Summary

In this article, you learned how to use `FutureAction` to register multiple database operations and execute them later in a single place.

Unlike [Query Future](/ef-core-query-future), `FutureAction` does not try to merge operations into fewer database round trips. Instead, it acts as a queue that lets you defer execution until you explicitly call `ExecuteFutureAction()` or `ExecuteFutureActionAsync()`.

`FutureAction` is especially useful when:

* You want to centralize execution of operations registered from different parts of your application
* You use the repository pattern and want each repository to register its own work
* You need to conditionally queue operations based on runtime logic
* You want to execute different types of operations together in a controlled way

This feature gives you more flexibility over when your database operations are executed while keeping your code organized and easier to maintain.