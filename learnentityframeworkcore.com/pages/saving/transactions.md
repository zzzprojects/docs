---
title: Using Transactions in EF Core
description: Learn how to use implicit and explicit transactions in EF Core to commit or roll back database operations as a single unit.
canonical: /saving/transactions
status: Published
lastmod: 2026-06-24
---

# Using Transactions in EF Core

EF Core uses transactions to ensure that database operations succeed or fail as a single unit.

A single call to `SaveChangesAsync()` is transactional by default. When more control is needed, you can use explicit transactions, savepoints, `TransactionScope`, or a shared transaction. EF Core also provides options that control automatic transactions and automatic savepoints.

## Use the Default Transaction

EF Core automatically wraps all changes in a single call to `SaveChangesAsync()` in a transaction.

```csharp
using var context = new AppDbContext();

var blog = await context.Blogs.FirstAsync(blog => blog.Name == "EF Core");

blog.Rating = 5;

context.Blogs.Add(new Blog
{
    Name = "Transactions in EF Core",
    Rating = 4
});

await context.SaveChangesAsync();
```

In this example, both the update and the insert are executed in the same transaction. If one operation fails, both changes are rolled back.

For more information about saving tracked changes, see [SaveChanges](/saving/save-changes).

## Use an Explicit Transaction

Use `BeginTransactionAsync()` when multiple operations or calls to `SaveChangesAsync()` must be committed or rolled back together.

```csharp
using var context = new AppDbContext();

using var transaction = context.Database.BeginTransactionAsync();

try
{
    var blog = new Blog
    {
        Name = "EF Core Transactions",
        Rating = 4
    };

    context.Blogs.Add(blog);

    await context.SaveChangesAsync();

    blog.Rating = 5;

    await context.SaveChangesAsync();

    await transaction.CommitAsync();
}
catch
{
    await transaction.RollbackAsync();
    throw;
}
```

`CommitAsync()` confirms both save operations.

If an error occurs, `RollbackAsync()` cancels the changes made inside the transaction.

## Use SaveChanges and ExecuteSql in the Same Transaction

Use an explicit transaction when tracked changes and raw SQL commands must succeed or fail together.

```csharp
using var context = new AppDbContext();

using var transaction = await context.Database.BeginTransactionAsync();

try
{
    context.Blogs.Add(new Blog
    {
        Name = "EF Core Transactions",
        Rating = 4
    });

    await context.SaveChangesAsync();

    await context.Database.ExecuteSqlAsync($"""
        UPDATE [Blogs]
        SET [Rating] = {5}
        WHERE [Name] = {"ZZZ Projects"}
    """);

    await transaction.CommitAsync();
}
catch
{
    await transaction.RollbackAsync();
    throw;
}
```

The tracked insert and the raw SQL command are committed or rolled back together.

For more information, see [ExecuteSql](/saving/execute-sql).

## Use Automatic Savepoints

EF Core automatically creates a savepoint before `SaveChangesAsync()` when a transaction is already active.

```csharp
using var context = new AppDbContext();

using var transaction = await context.Database.BeginTransactionAsync();

var blog = new Blog
{
    Name = "EF Core Savepoints",
    Rating = 4
};

context.Blogs.Add(blog);

await context.SaveChangesAsync();

blog.Rating = 5;

await context.SaveChangesAsync();

await transaction.CommitAsync();
```

Because the transaction is already active, EF Core creates an automatic savepoint before `SaveChangesAsync()` when the provider supports savepoints.

If `SaveChangesAsync()` fails, EF Core rolls back to that savepoint. The transaction remains available for a possible correction or retry.

## Create a Custom Savepoint

Use `CreateSavepointAsync()` when the application needs a specific recovery point inside a transaction.

```csharp
using var context = new AppDbContext();

using var transaction = await context.Database.BeginTransactionAsync();

try
{
    context.Blogs.Add(new Blog
    {
        Name = "Main Blog",
        Rating = 5
    });

    await context.SaveChangesAsync();

    await transaction.CreateSavepointAsync("BeforeOptionalBlog");

    var optionalBlog = new Blog
    {
        Name = "Optional Blog",
        Rating = 4
    };

    try
    {
        context.Blogs.Add(optionalBlog);

        await context.SaveChangesAsync();
    }
    catch (DbUpdateException)
    {
        await transaction.RollbackToSavepointAsync("BeforeOptionalBlog");

        context.Entry(optionalBlog).State = EntityState.Detached;
    }

    await transaction.CommitAsync();
}
catch
{
    await transaction.RollbackAsync();
    throw;
}
```

If the optional insert fails, the transaction rolls back to `BeforeOptionalBlog`. The first insert remains inside the transaction and can still be committed.

The example detaches `optionalBlog` so the failed insert is not attempted again. Other recovery strategies may require different `ChangeTracker` cleanup.

## Transaction-Related Options

EF Core also exposes options for controlling automatic transactions and automatic savepoints.

### AutoTransactionBehavior

`AutoTransactionBehavior` controls when EF Core creates a transaction automatically for `SaveChangesAsync()`.

```csharp
using var context = new AppDbContext();

context.Database.AutoTransactionBehavior = AutoTransactionBehavior.WhenNeeded;
```

Available values:

* `WhenNeeded` — creates a transaction when EF Core determines that one is required; this is the default
* `Always` — always creates a transaction when no user transaction exists
* `Never` — prevents EF Core from creating a transaction automatically

Use `Never` with caution. If saving requires several database commands, a failure may leave only part of the changes applied.

### AutoSavepointsEnabled

`AutoSavepointsEnabled` controls whether EF Core creates savepoints automatically inside an existing transaction.

```csharp
using var context = new AppDbContext();

context.Database.AutoSavepointsEnabled = false;
```

The default value is `true`.

Disable automatic savepoints only when the application provides its own recovery behavior.

### AutoTransactionsEnabled

`AutoTransactionsEnabled` also controls whether `SaveChangesAsync()` creates transactions automatically.

```csharp
using var context = new AppDbContext();

context.Database.AutoTransactionsEnabled = false;
```

`AutoTransactionsEnabled` is obsolete. Use `AutoTransactionBehavior` in new code.

## Use TransactionScope

`TransactionScope` creates an ambient transaction for supported operations inside its scope.

```csharp
using System.Transactions;

using var scope = new TransactionScope(TransactionScopeAsyncFlowOption.Enabled);

using var context = new AppDbContext();

context.Blogs.Add(new Blog
{
    Name = "TransactionScope Blog",
    Rating = 4
});

await context.SaveChangesAsync();

await context.Database.ExecuteSqlAsync($"""
    UPDATE [Blogs]
    SET [Rating] = {5}
    WHERE [Name] = {"TransactionScope Blog"}
""");

scope.Complete();
```

`scope.Complete()` marks the transaction for commit when the scope is disposed.

Without `Complete()`, the transaction is rolled back.

Use `TransactionScopeAsyncFlowOption.Enabled` with asynchronous operations. Provider support and distributed transaction support can vary by platform.

## Share a Transaction Between DbContext Instances

Multiple `DbContext` instances can share a transaction when they use the same relational connection.

The contexts must accept externally configured `DbContextOptions` so they can use the same connection.

The following example uses SQL Server, but the same idea applies to relational providers that support shared transactions.

```csharp
using var connection = new SqlConnection(connectionString);

await connection.OpenAsync();

var options = new DbContextOptionsBuilder<AppDbContext>()
    .UseSqlServer(connection)
    .Options;

using var context1 = new AppDbContext(options);

using var transaction = await context1.Database.BeginTransactionAsync();

try
{
    context1.Blogs.Add(new Blog
    {
        Name = "Shared Transaction Blog",
        Rating = 4
    });

    await context1.SaveChangesAsync();

    using var context2 = new AppDbContext(options);

    await context2.Database.UseTransactionAsync(
        transaction.GetDbTransaction());

    var blog = await context2.Blogs.FirstAsync(blog => blog.Name == "Shared Transaction Blog");

    blog.Rating = 5;

    await context2.SaveChangesAsync();

    await transaction.CommitAsync();
}
catch
{
    await transaction.RollbackAsync();
    throw;
}
```

Both contexts use the same `DbConnection` and `DbTransaction`.

Shared transactions are available only with relational database providers.

## Important Behavior

* A single call to `SaveChangesAsync()` is transactional by default when the provider supports transactions.
* An explicit transaction can include multiple `SaveChangesAsync()` calls or raw SQL commands.
* Disposing an uncommitted transaction rolls it back.
* Savepoints, shared transactions, and `TransactionScope` depend on provider support.

Manually controlled transactions are not directly compatible with implicitly invoked retrying execution strategies.

When connection resiliency is enabled, execute the complete transaction through the configured execution strategy.

## How Transactions Work

An explicit transaction begins with `BeginTransactionAsync()` and remains active until it is committed, rolled back, or disposed.

`CommitAsync()` permanently applies its operations. `RollbackAsync()` discards them.

A savepoint creates a recovery point inside the transaction. Rolling back to it discards operations performed after that point without automatically discarding earlier operations.

## When to Use an Explicit Transaction

Use an explicit transaction when:

* several `SaveChangesAsync()` calls must succeed or fail together
* tracked changes and raw SQL belong to the same unit of work
* multiple contexts or data-access technologies must share a transaction
* the application needs custom savepoints

## When Not to Use an Explicit Transaction

Avoid an explicit transaction when:

* one `SaveChangesAsync()` call already provides the required atomicity
* the transaction would remain open during slow external work
* the provider does not support the required behavior
* retrying execution strategies are used without transaction coordination

Keep explicit transactions as short as possible.

## Common Pitfalls

### Creating a Transaction for Every SaveChanges Call

A single `SaveChangesAsync()` call is already transactional when the provider supports transactions.

Add an explicit transaction only when the unit of work extends beyond that call.

### Committing Too Early

Call `CommitAsync()` only after every operation in the unit of work succeeds.

```csharp
await context.SaveChangesAsync();
await transaction.CommitAsync();

// This command is outside the committed transaction.
await context.Database.ExecuteSqlAsync($"""
    UPDATE [Blogs]
    SET [Rating] = {5}
    WHERE [Name] = {"EF Core"}
""");
```

Commands executed after the commit are not protected by the completed transaction.

### Keeping a Transaction Open During External Work

Avoid keeping a database transaction open while waiting for slow external operations, such as HTTP calls, user input, or long-running background work.

A database transaction can roll back database changes, but it cannot undo external side effects that have already completed.

### Forgetting Async Flow with TransactionScope

A `TransactionScope` does not flow correctly across `await` calls unless async flow is enabled.

```csharp
using var scope = new TransactionScope(TransactionScopeAsyncFlowOption.Enabled);
```

### Assuming Every Provider Supports the Same Features

Savepoints, shared transactions, and `TransactionScope` support can vary between providers.

Test the required behavior with the provider used by the application.

### Using Savepoints with SQL Server MARS

EF Core does not create savepoints when SQL Server Multiple Active Result Sets, or MARS, is enabled.

If `SaveChangesAsync()` fails in that situation, the transaction may be left in an unknown state.

### Disabling Automatic Transactions Without a Recovery Plan

Do not set `AutoTransactionBehavior` to `Never` merely to avoid transaction overhead.

If saving requires multiple commands, a failure may leave only part of the changes applied.

## External Resources - Transactions

The following videos are useful if you want to see practical examples of EF Core transactions, especially the default `SaveChangesAsync()` transaction and explicit transactions with commit and rollback.

### Video 1 - Working With Database Transactions In EF Core

<iframe width="560" height="315" src="https://www.youtube.com/embed/25H84BXcr9M" title="Working With Database Transactions In EF Core - YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

This video by Milan Jovanović explains the default transaction behavior in EF Core and shows when an explicit transaction becomes useful, especially when an operation involves multiple calls to `SaveChangesAsync()`.

The video is most useful for understanding explicit transactions, `SaveChangesAsync()`, commit, rollback, and practical transaction flow. The article sections above cover more specialized topics such as `TransactionScope`, shared transactions, and transaction-related options.

Key timestamps:

* [1:21](https://www.youtube.com/watch?v=25H84BXcr9M&t=81) — Default transaction behavior in EF Core
* [1:57](https://www.youtube.com/watch?v=25H84BXcr9M&t=117) — What happens when you have multiple calls to `SaveChanges`
* [4:31](https://www.youtube.com/watch?v=25H84BXcr9M&t=271) — Creating a new transaction with EF Core
* [5:52](https://www.youtube.com/watch?v=25H84BXcr9M&t=352) — Using transactions in an application
* [7:57](https://www.youtube.com/watch?v=25H84BXcr9M&t=477) — How transactions work in practice
* [12:53](https://www.youtube.com/watch?v=25H84BXcr9M&t=773) — Using EF Core's `CurrentTransaction`

### Video 2 - The EF Core Mistake That Leaves Your Data Out of Sync

<iframe width="560" height="315" src="https://www.youtube.com/embed/8ZuZrO-jm-8" title="The EF Core Mistake That Leaves Your Data Out of Sync - YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

This video by Round The Code shows a practical consistency problem that can happen when an operation is split across multiple steps and one step fails before the transaction is committed.

It is useful for understanding why an explicit transaction can be needed when the default transaction around a single `SaveChangesAsync()` call is not enough. The video also shows rollback handling with `try/catch`.

The example includes an external operation, so remember that a database transaction only rolls back database changes. It cannot undo external side effects that have already completed, such as a sent email.

Key timestamps:

* [0:00](https://www.youtube.com/watch?v=8ZuZrO-jm-8&t=0) — Introduction to the data consistency problem
* [1:25](https://www.youtube.com/watch?v=8ZuZrO-jm-8&t=85) — Using an explicit transaction with EF Core
* [3:05](https://www.youtube.com/watch?v=8ZuZrO-jm-8&t=185) — Rolling back when an intermediate operation fails
* [4:06](https://www.youtube.com/watch?v=8ZuZrO-jm-8&t=246) — Using `try/catch` with `RollbackAsync()`
* [6:45](https://www.youtube.com/watch?v=8ZuZrO-jm-8&t=405) — Default transaction behavior with `SaveChangesAsync()`

## Summary

EF Core automatically uses a transaction for a single call to `SaveChangesAsync()` when the database provider supports transactions.

Use an explicit transaction when several operations must succeed or fail together, such as multiple save operations, raw SQL commands, shared contexts, or custom savepoints.

Key points:

* `BeginTransactionAsync()` starts an explicit transaction.
* `CommitAsync()` applies the operations permanently.
* `RollbackAsync()` discards the operations.
* EF Core creates automatic savepoints inside an existing transaction when the provider supports savepoints.
* Transaction features can vary by database provider.

For most applications, the default `SaveChangesAsync()` transaction is sufficient.

## Related Articles

* [SaveChanges](/saving/save-changes) — how EF Core persists tracked changes and uses transactions by default
* [ExecuteSql](/saving/execute-sql) — how to execute raw SQL commands inside a transaction
* [Adding Data](/saving/adding-data) — how to insert tracked entities
* [Updating Data](/saving/modifying-data) — how to update tracked entities

## FAQ

### Does SaveChangesAsync use a transaction automatically?

Yes. When the database provider supports transactions, EF Core executes all commands generated by a single `SaveChangesAsync()` call inside a transaction.

### When should I use BeginTransactionAsync?

Use `BeginTransactionAsync()` when the unit of work includes multiple save operations, raw SQL commands, shared contexts, or custom savepoints.

### Do I need an explicit transaction for one SaveChangesAsync call?

Usually, no. A single `SaveChangesAsync()` call is already transactional by default when the provider supports transactions.

### What happens if a transaction is disposed without CommitAsync?

The transaction is rolled back when it is disposed without being committed.

### What is a savepoint?

A savepoint is a recovery point inside a transaction. Rolling back to it discards later operations while preserving the work performed before it.

### Can multiple DbContext instances share a transaction?

Yes, when using a relational database provider. The contexts must share the same `DbConnection`.
