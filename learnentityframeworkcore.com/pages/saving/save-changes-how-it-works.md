---
title: How SaveChanges Works in EF Core
description: Learn what Entity Framework Core does internally when SaveChangesAsync is called, including change detection, command generation, transactions, savepoints, concurrency checks, store-generated values, and accepting changes after a successful save.
canonical: /saving/save-changes-how-it-works
status: Published
lastmod: 2026-06-28
---

# How SaveChanges Works in EF Core

`SaveChangesAsync()` persists changes that EF Core is already tracking in the current `DbContext`.

This article explains what EF Core does internally when `SaveChanges()` or `SaveChangesAsync()` is called, including change detection, command generation, transactions, concurrency checks, store-generated values, and what happens after the save succeeds or fails.

## How SaveChanges Works Under the Hood

When you call `SaveChanges()` or `SaveChangesAsync()`, EF Core performs the following steps:

* Check whether any changes need to be saved.

  * Call [`TryDetectChanges()`](https://github.com/dotnet/efcore/blob/3cb71a65a7bdd2acdb5545a1262d38b07135fbe7/src/EFCore/DbContext.cs#L652).
  * Call [`ChangeTracker.DetectChanges()`](https://github.com/dotnet/efcore/blob/3cb71a65a7bdd2acdb5545a1262d38b07135fbe7/src/EFCore/DbContext.cs#L695) if [`ChangeTracker.AutoDetectChangesEnabled`](https://github.com/dotnet/efcore/blob/3cb71a65a7bdd2acdb5545a1262d38b07135fbe7/src/EFCore/DbContext.cs#L693) is enabled.
  * If no changes are detected, [returns `0`](https://github.com/dotnet/efcore/blob/3cb71a65a7bdd2acdb5545a1262d38b07135fbe7/src/EFCore/ChangeTracking/Internal/StateManager.cs#L1391) immediately.
  
* Prepare the database commands.

  * [Create modification commands](https://github.com/dotnet/efcore/blob/3cb71a65a7bdd2acdb5545a1262d38b07135fbe7/src/EFCore.Relational/Update/Internal/CommandBatchPreparer.cs#L208C1-L208C92) for all pending changes.
  * [Sort commands](https://github.com/dotnet/efcore/blob/3cb71a65a7bdd2acdb5545a1262d38b07135fbe7/src/EFCore.Relational/Update/Internal/CommandBatchPreparer.cs#L371) to respect foreign key dependencies.
  * Batch commands whenever possible.
 
* Open the database connection if it is not already open.

* Ensure a transaction is available.

  * Use the current transaction if one exists.
  * Otherwise, [create a transaction](https://github.com/dotnet/efcore/blob/3cb71a65a7bdd2acdb5545a1262d38b07135fbe7/src/EFCore.Relational/Update/Internal/BatchExecutor.cs#L78) if needed.
  * If using an existing transaction, [create a savepoint](https://github.com/dotnet/efcore/blob/3cb71a65a7bdd2acdb5545a1262d38b07135fbe7/src/EFCore.Relational/Update/Internal/BatchExecutor.cs#L88) if the provider supports it and savepoints are enabled.

* Execute the command batches.

* Verify the number of affected rows.

  * If the number of affected rows does not match the expected count, throw a `DbUpdateConcurrencyException`.

* Retrieve store-generated values when needed.

  * Identity values.
  * Computed columns.
  * Default values.

* If the operation succeeds:

  * Commit the transaction if EF Core created it.
  * Otherwise, release the savepoint if one was created.
  * Call [`AcceptChanges()`](https://github.com/dotnet/efcore/blob/3cb71a65a7bdd2acdb5545a1262d38b07135fbe7/src/EFCore/ChangeTracking/Internal/StateManager.cs#L1403) if [`acceptAllChangesOnSuccess`](https://github.com/dotnet/efcore/blob/3cb71a65a7bdd2acdb5545a1262d38b07135fbe7/src/EFCore/ChangeTracking/Internal/StateManager.cs#L1401) is `true`.
  * Return the number of state entries written to the database.

* If an error occurs:

  * Roll back the transaction if EF Core created it.
  * Otherwise, roll back to the savepoint if one was created.
  * Call [`DiscardStoreGeneratedValues()`](https://github.com/dotnet/efcore/blob/3cb71a65a7bdd2acdb5545a1262d38b07135fbe7/src/EFCore/ChangeTracking/Internal/StateManager.cs#L1412) for every entity involved in the failed save operation.
  * Rethrow the exception.

* Close the database connection if EF Core opened it.

The rest of this article explains the most important parts of this pipeline in more detail.

### A Minimal Tracked Change

A normal tracked change can look very small:

```csharp
using var context = new AppDbContext();

var product = await context.Products.FirstAsync();

product.Price = 25;

await context.SaveChangesAsync();
```

The code changes one property and saves the change.

Internally, EF Core must inspect the tracked entity, detect what changed, prepare the database command, execute it safely, check the result, and accept the tracked changes after the save succeeds.

For a deeper explanation of how EF Core tracks entity states and changed values before saving, see [How Change Tracker Works](/saving/change-tracker-how-it-works).

For the general `SaveChangesAsync()` workflow, see [SaveChanges](/saving/save-changes). This article focuses on what happens under the hood.

### Step 1: Check Whether Any Changes Need to Be Saved

Before saving, EF Core checks whether the current `DbContext` has any pending changes.

EF Core uses the `ChangeTracker` to determine whether there are changes to save. This corresponds to the [TryDetectChanges()](https://github.com/dotnet/efcore/blob/3cb71a65a7bdd2acdb5545a1262d38b07135fbe7/src/EFCore/DbContext.cs#L652) check shown in the pipeline above.

As part of this check, EF Core can call [ChangeTracker.DetectChanges()](https://github.com/dotnet/efcore/blob/3cb71a65a7bdd2acdb5545a1262d38b07135fbe7/src/EFCore/DbContext.cs#L695) when [ChangeTracker.AutoDetectChangesEnabled](https://github.com/dotnet/efcore/blob/3cb71a65a7bdd2acdb5545a1262d38b07135fbe7/src/EFCore/DbContext.cs#L693) is enabled.

In the previous example, the product was loaded and tracked by the current `DbContext`. When the `Price` property changed, EF Core could compare the current value with the original tracked value and detect that the entity had a pending update.

A simplified view looks like this:

```text
Original value:
Price = 20

Current value:
Price = 25

Detected change:
Price was modified
```

If no changes are detected, `SaveChanges()` or `SaveChangesAsync()` [returns `0`](https://github.com/dotnet/efcore/blob/3cb71a65a7bdd2acdb5545a1262d38b07135fbe7/src/EFCore/ChangeTracking/Internal/StateManager.cs#L1391) immediately.

```csharp
var result = await context.SaveChangesAsync();
```

If the current `DbContext` has no pending inserts, updates, or deletes, the result is `0`.

### Step 2: Prepare the Database Commands

After EF Core knows which changes need to be saved, it prepares the database commands.

First, EF Core [creates modification commands](https://github.com/dotnet/efcore/blob/3cb71a65a7bdd2acdb5545a1262d38b07135fbe7/src/EFCore.Relational/Update/Internal/CommandBatchPreparer.cs#L208C1-L208C92) for all pending changes.

A modification command represents database work such as an insert, update, or delete. The command is based on the tracked entries in the `ChangeTracker` and the EF Core model.

EF Core does not build commands from entity state alone.

It also uses:

* key values
* property mappings
* modified properties
* original values
* relationship changes
* concurrency tokens, when configured
* foreign key dependencies

Next, EF Core [sorts commands](https://github.com/dotnet/efcore/blob/3cb71a65a7bdd2acdb5545a1262d38b07135fbe7/src/EFCore.Relational/Update/Internal/CommandBatchPreparer.cs#L371) to respect foreign key dependencies.

For example, an entity with a foreign key may depend on another row being inserted first. EF Core can order the commands so those dependencies are respected.

Finally, EF Core can batch commands whenever possible, so multiple commands can be sent to the database more efficiently.

The exact commands, ordering, and batching behavior depend on the database provider, model configuration, and changes being saved.

### Step 3: Open the Database Connection

After EF Core prepare batch to be saved, it needs a database connection to execute the save operation.

If the database connection is not already open, EF Core opens it.

If the application already opened the connection, EF Core uses the existing open connection.

EF Core only closes the connection if EF Core opened it.

Most applications do not need to manage this manually.

### Step 4: Ensure a Transaction Is Available

Before executing the database commands, EF Core ensures that a transaction is available when needed.

If the current `DbContext` is already using a transaction, EF Core uses the current transaction.

If there is no current transaction, EF Core can [create a transaction](https://github.com/dotnet/efcore/blob/3cb71a65a7bdd2acdb5545a1262d38b07135fbe7/src/EFCore.Relational/Update/Internal/BatchExecutor.cs#L78) for the save operation.

This matters because one call to `SaveChanges()` or `SaveChangesAsync()` can produce multiple database commands. A transaction helps those commands succeed or fail together.

If EF Core is using an existing transaction, it can [create a savepoint](https://github.com/dotnet/efcore/blob/3cb71a65a7bdd2acdb5545a1262d38b07135fbe7/src/EFCore.Relational/Update/Internal/BatchExecutor.cs#L88) before saving, when the provider supports it and savepoints are enabled.

If the save succeeds, the transaction can [commit](https://github.com/dotnet/efcore/blob/3cb71a65a7bdd2acdb5545a1262d38b07135fbe7/src/EFCore.Relational/Update/Internal/BatchExecutor.cs#L103) if created or [release](https://github.com/dotnet/efcore/blob/3cb71a65a7bdd2acdb5545a1262d38b07135fbe7/src/EFCore.Relational/Update/Internal/BatchExecutor.cs#L136) if a savepoint have been used.

If the save fails, EF Core can roll back to the savepoint instead of rolling back the entire transaction.

For more about default transactions, explicit transactions, and savepoints, see [Transactions](/saving/transactions).

### Step 5: Execute the Command Batches

After the database commands are prepared and the connection ready, EF Core executes the command batches.

A single call to `SaveChanges()` or `SaveChangesAsync()` can result in one command or multiple commands, depending on the pending changes.

The exact SQL, batching behavior, and number of database round trips depend on the database provider and the changes being saved. When possible, batching reduces separate round trips.

At this point, the changes are no longer only tracked in memory. EF Core is executing the database work.

### Step 6: Verify the Number of Affected Rows

After an update or delete command runs, the database reports how many rows were affected.

EF Core checks that result against the number of rows it expected to affect.

For a tracked update or delete, EF Core usually expects the command to affect the row represented by the tracked entry.

A simplified update can look like this:

```sql
UPDATE [Products]
SET [Price] = @p0
WHERE [Id] = @p1;
```

If the command affects the expected row, the save operation continues.

If the number of affected rows does not match the expected count, EF Core can throw `DbUpdateConcurrencyException`.

This affected-row check is one way EF Core detects optimistic concurrency conflicts when concurrency tokens are configured.

For example, EF Core can include the original token value in the update or delete condition:

```sql
UPDATE [Products]
SET [Price] = @p0
WHERE [Id] = @p1
  AND [RowVersion] = @p2;
```

If another user or process changed the row first, the token no longer matches. The command can affect zero rows, and EF Core throws `DbUpdateConcurrencyException`.

EF Core detects the conflict, but the application decides how to resolve it.

For conflict detection and resolution strategies, see [Concurrency](/saving/concurrency).

### Step 7: Retrieve Store-Generated Values

After the command batches succeed, EF Core retrieves store-generated values when needed.

Common examples include:

* identity values
* computed columns
* default values
* updated `rowversion` values, when configured

EF Core applies the returned values back to the tracked entities in the current `DbContext`.

This keeps tracked entities aligned with the database instead of leaving temporary or outdated values in memory.

### Step 8: Complete the Save Operation

After the database work succeeds, EF Core completes the save operation.

If EF Core created the transaction, it [commits the transaction](https://github.com/dotnet/efcore/blob/3cb71a65a7bdd2acdb5545a1262d38b07135fbe7/src/EFCore.Relational/Update/Internal/BatchExecutor.cs#L103).

If EF Core used an existing transaction and created a savepoint, it [releases the savepoint](https://github.com/dotnet/efcore/blob/3cb71a65a7bdd2acdb5545a1262d38b07135fbe7/src/EFCore.Relational/Update/Internal/BatchExecutor.cs#L136) after the save succeeds.

EF Core then accepts the tracked changes when `acceptAllChangesOnSuccess` is `true`.

This updates the `ChangeTracker` so the saved entries are no longer treated as pending changes.

In application code, the related public API is `ChangeTracker.AcceptAllChanges()`. Most applications do not call it manually because `SaveChanges()` and `SaveChangesAsync()` accept the changes automatically after a successful save.

You can skip this automatic step with:

```csharp
await context.SaveChangesAsync(acceptAllChangesOnSuccess: false);
```

In that case, EF Core sends the commands to the database, but it does not automatically accept the tracked changes. The application can call [`AcceptAllChanges()`](https://github.com/dotnet/efcore/blob/3cb71a65a7bdd2acdb5545a1262d38b07135fbe7/src/EFCore/ChangeTracking/Internal/StateManager.cs#L1403) later if needed.

Finally, `SaveChanges()` or `SaveChangesAsync()` returns the number of state entries written to the database.

### Step 9: Handle Save Failures

If an error occurs during `SaveChanges()` or `SaveChangesAsync()`, EF Core stops the save operation and rethrows the exception.

If EF Core created the transaction, it rolls back the transaction.

If EF Core used an existing transaction and created a savepoint, it rolls back to the savepoint.

EF Core calls [`DiscardStoreGeneratedValues()`](https://github.com/dotnet/efcore/blob/3cb71a65a7bdd2acdb5545a1262d38b07135fbe7/src/EFCore/ChangeTracking/Internal/StateManager.cs#L1412) for every entity involved in the failed save operation.

This prevents values returned during a failed save from being kept as if the save had succeeded.

The tracked changes are not accepted after a failed save. The current `DbContext` can still contain entries in states such as `Added`, `Modified`, or `Deleted`.

Depending on the error, the application may need to retry, reload values, detach entries, or discard the current `DbContext`.

### Step 10: Close the Database Connection

After the save operation finishes, EF Core closes the database connection if EF Core opened it.

If the application opened the connection manually, EF Core does not take ownership of that connection lifecycle.

Most applications do not need to manage this directly. EF Core opens and closes the connection around `SaveChanges()` or `SaveChangesAsync()` when needed.

## External Resources

The following resources are useful if you want to understand the `SaveChanges()` pipeline and the `ChangeTracker` part of that pipeline.

### Microsoft Docs - DbContext.SaveChangesAsync Method

For the official API reference, see the Microsoft documentation for [`DbContext.SaveChangesAsync`](https://learn.microsoft.com/en-us/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync).

The documentation confirms that `SaveChangesAsync()` can automatically call `DetectChanges()` before saving, unless automatic change detection has been disabled.

### Video - How does EF Core keeps track of changes?

<iframe width="560" height="315" src="https://www.youtube.com/embed/uXDYEBexlYk" title="How does EF Core keeps track of changes? - YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

Hubert Mijalski explains how the EF Core `ChangeTracker` tracks entity states and how that tracked state is used when `SaveChanges()` sends changes to the database.

This video is especially useful for understanding pending changes, entity states, generated SQL, and why calling `Update()` unnecessarily can mark more properties as modified than expected.

It does not cover the full `SaveChanges()` pipeline. Transactions, savepoints, batching, affected rows, store-generated values, and `AcceptAllChanges()` are not discussed.

Key sections:

* [00:00](https://www.youtube.com/watch?v=uXDYEBexlYk&t=0) — Introduction to the `ChangeTracker` and its role before `SaveChanges()`
* [01:30](https://www.youtube.com/watch?v=uXDYEBexlYk&t=90) — Entity states when adding and removing entities
* [03:00](https://www.youtube.com/watch?v=uXDYEBexlYk&t=180) — Why `Update()` is different and can be risky
* [06:20](https://www.youtube.com/watch?v=uXDYEBexlYk&t=380) — Generated SQL and the difference between tracked changes and explicit `Update()`

## Summary

`SaveChanges()` and `SaveChangesAsync()` run the EF Core save pipeline for the changes tracked by the current `DbContext`.

Key points:

* EF Core checks whether there are changes to save and can call `DetectChanges()` when automatic change detection is enabled.
* EF Core prepares, sorts, batches, and executes modification commands.
* EF Core opens the database connection if needed and ensures that a transaction is available.
* When EF Core uses an existing transaction, it can create a savepoint if the provider supports it and savepoints are enabled.
* EF Core verifies affected rows and can throw `DbUpdateConcurrencyException`.
* EF Core retrieves store-generated values when needed.
* After a successful save, EF Core accepts the tracked changes when `acceptAllChangesOnSuccess` is `true`.
* If the save fails, EF Core rolls back the transaction or savepoint, discards store-generated values, and rethrows the exception.

Use this article when you want to understand what happens inside the `SaveChanges()` and `SaveChangesAsync()` pipeline.

For basic usage examples, see [SaveChanges](/saving/save-changes).

## Related Articles

If you want to continue through the EF Core saving workflow, these pages are the best next steps:

* [SaveChanges](/saving/save-changes) — how to use `SaveChanges()` and `SaveChangesAsync()` in common saving scenarios
* [Change Tracker](/saving/change-tracker) — how EF Core tracks entities before they are saved
* [How Change Tracker Works](/saving/change-tracker-how-it-works) — how EF Core tracks states, values, and changes internally
* [Transactions](/saving/transactions) — how EF Core uses transactions, explicit transactions, and savepoints
* [Concurrency](/saving/concurrency) — how EF Core detects and handles conflicting updates

## FAQ

### Does SaveChangesAsync always call DetectChanges?

`SaveChangesAsync()` can call `DetectChanges()` as part of the save process when automatic change detection is enabled.

If `AutoDetectChangesEnabled` is disabled, the application may need to call `DetectChanges()` manually before saving.

### What happens if there are no changes to save?

If no changes are detected, `SaveChanges()` or `SaveChangesAsync()` returns `0`.

No database commands are executed because there are no pending changes to save.

### Does SaveChangesAsync use a transaction?

For relational database providers, `SaveChangesAsync()` uses a transaction when a transaction is needed for the save operation.

If a transaction already exists, EF Core can use that transaction and create a savepoint when the provider supports it and savepoints are enabled.

### What does SaveChangesAsync return?

`SaveChangesAsync()` returns the number of state entries written to the database.

This is not necessarily the same as the number of database rows affected, because EF Core reports entries written from the `ChangeTracker`, not raw database row counts.

### What happens after SaveChangesAsync succeeds?

EF Core can retrieve store-generated values, commit the transaction if it created one, release a savepoint if one was created, and accept the tracked changes when `acceptAllChangesOnSuccess` is `true`.

After the changes are accepted, the saved entries are no longer treated as pending changes by the current `DbContext`.

### What happens if SaveChangesAsync fails?

EF Core rolls back the transaction it created, or rolls back to a savepoint if one was created inside an existing transaction.

It also discards store-generated values for the failed save operation and rethrows the exception. The application must decide whether to retry, reload values, detach entries, or discard the current `DbContext`.
