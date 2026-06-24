---
title: EF Core Concurrency: Database Wins
description: How to resolve EF Core concurrency conflicts by keeping the database values and discarding the current changes
canonical: /saving/concurrency-database-wins
status: Published
lastmod: 2026-06-24
---

# EF Core Concurrency: Database Wins

Database Wins keeps the values currently stored in the database and discards the current application changes in conflicts and retrying `SaveChangesAsync()`.

## Resolve a Conflict with Database Wins

Use `GetDatabaseValuesAsync()` to read the latest database values and replace the current entity values.

The example retries `SaveChangesAsync()` until the concurrency conflict is resolved.

```csharp
var saved = false;

while (!saved)
{
    try
    {
        await context.SaveChangesAsync();
        saved = true;
    }
    catch (DbUpdateConcurrencyException ex)
    {
        foreach (var entry in ex.Entries)
        {
            var databaseValues = await entry.GetDatabaseValuesAsync();

            if (databaseValues == null)
            {
                entry.State = EntityState.Detached;
                continue;
            }

            entry.CurrentValues.SetValues(databaseValues);
            entry.OriginalValues.SetValues(databaseValues);
            entry.State = EntityState.Unchanged;
        }
    }
}
```

In this example:

* `GetDatabaseValuesAsync()` reads the latest values from the database.
* `CurrentValues.SetValues(databaseValues)` replaces the current application values with the database values.
* `OriginalValues.SetValues(databaseValues)` refreshes the original values tracked by EF Core.
* `EntityState.Unchanged` marks the entity as no longer modified.
* The `while` loop retries `SaveChangesAsync()` after the conflict is handled.

After this code runs, the entity in memory matches the database, and any application changes that caused the conflict are discarded.

If there are other pending changes in the `DbContext`, they are saved when `SaveChangesAsync()` succeeds.

## Example Scenario

Assume a product is loaded with this value:

```text
Price = 25.00
```

The application changes the price to:

```text
Price = 29.99
```

Before the application saves, another user or process changes the same product in the database:

```text
Price = 27.50
```

When `SaveChangesAsync()` runs, EF Core detects that the row was changed after it was loaded and throws `DbUpdateConcurrencyException`.

With Database Wins, the final value kept by the application is the database value:

```text
Price = 27.50
```

The application value is discarded.

## How Database Wins Works

Database Wins works by replacing the current entity values with the latest values from the database.

The important part is that both the current values and the original values are updated.

```csharp
entry.CurrentValues.SetValues(databaseValues);
entry.OriginalValues.SetValues(databaseValues);
```

The current values are updated so the entity in memory shows the latest database values.

The original values are updated so EF Core no longer uses the old original concurrency token value.

After that, the entity can be marked as unchanged:

```csharp
entry.State = EntityState.Unchanged;
```

This tells EF Core that the entity now matches the database and does not need to be saved again.

## When to Use Database Wins

Use Database Wins when the database should be treated as the source of truth.

This strategy is useful when the current application changes can be safely discarded.

Common examples include:

* data reviewed or approved by another user
* values updated by a background process
* records where the latest database state should not be overwritten
* screens where it is safer to reload data than to force the current changes

Database Wins is usually a good fit when the application should accept the latest saved values without manual merging.

## When Not to Use Database Wins

Do not use Database Wins when the current changes must still be reviewed or saved.

Use another strategy when the application needs to keep the current application changes.

For example, Database Wins is usually not the best choice when:

* the user entered important data that should not be silently discarded
* both versions need to be compared before deciding
* some values should come from the database and some values should come from the current application
* the user should decide which values are correct

In those cases, use [Client Wins](/saving/concurrency-client-wins) or [Custom Resolution](/saving/concurrency-custom-resolution).

## Handle Deleted Rows

If `GetDatabaseValuesAsync()` returns `null`, the row no longer exists in the database.

```csharp
var databaseValues = await entry.GetDatabaseValuesAsync();

if (databaseValues == null)
{
    entry.State = EntityState.Detached;
    continue;
}
```

In this case, there are no database values to copy into the entity.

Marking the entity as `Detached` tells EF Core to stop tracking it in the current context.

This is useful when another user or process deleted the row before the current application changes were saved.

After the entity is detached, EF Core will not try to save it again in the same context.

## Important Behavior

Database Wins discards the current application changes.

Important points:

* `GetDatabaseValuesAsync()` reads the latest values from the database.
* `CurrentValues.SetValues(databaseValues)` replaces the values the application tried to save.
* `OriginalValues.SetValues(databaseValues)` refreshes the values EF Core uses for concurrency checking.
* `EntityState.Unchanged` tells EF Core that the entity now matches the database.
* If `databaseValues` is `null`, the row was deleted by another user or process.
* Database Wins does not merge values property by property.
* Database Wins does not retry the original update.
* Database Wins resolves the current conflict, but it does not prevent future conflicts.

For a broader overview of concurrency conflict handling, see [Concurrency](/saving/concurrency).

## Common Pitfalls

Be careful with the following mistakes.

### Expecting the Current Changes to Be Saved

Database Wins keeps the database values.

```csharp
entry.CurrentValues.SetValues(databaseValues);
```

The current application values are replaced with the latest database values.

If the current changes must still be saved, use [Client Wins](/saving/concurrency-client-wins) instead.

If the application needs to compare values before deciding, use [Custom Resolution](/saving/concurrency-custom-resolution).

### Updating Only Current Values

Do not update only the current values.

```csharp
entry.CurrentValues.SetValues(databaseValues);
```

If you only update `CurrentValues`, EF Core may still keep old original values for concurrency tracking.

Refresh both current values and original values:

```csharp
entry.CurrentValues.SetValues(databaseValues);
entry.OriginalValues.SetValues(databaseValues);
```

This keeps the tracked entity consistent with the database.

### Forgetting to Retry SaveChangesAsync

Refreshing `CurrentValues` and `OriginalValues` does not save the changes not in conflicts by itself.

After the original values are refreshed, the application must call `SaveChangesAsync()` again.

That is why Database Wins is commonly implemented inside a retry loop.

### Ignoring Deleted Rows

`GetDatabaseValuesAsync()` can return `null`.

```csharp
if (databaseValues == null)
{
    entry.State = EntityState.Detached;
    continue;
}
```

This means the row was deleted before the current changes were saved.

Do not call `SetValues()` when `databaseValues` is `null`.

## Summary

Database Wins keeps the latest database values and discards the current application changes that caused the conflict.

Use this strategy when the database should be treated as the source of truth and the current changes can be safely replaced.

Key points:

* `GetDatabaseValuesAsync()` reads the latest values from the database.
* `CurrentValues` are replaced with the database values.
* `OriginalValues` are refreshed so EF Core no longer uses the old original concurrency token value.
* `SaveChangesAsync()` must be retried after the current and original values are refreshed.
* If `GetDatabaseValuesAsync()` returns `null`, the row no longer exists in the database.

Use another strategy when the current changes must be preserved or when the application needs to compare both versions before deciding.

## Related Articles

If you want to continue with EF Core concurrency conflict handling, these articles are the best next steps:

* [Concurrency](/saving/concurrency) — overview of EF Core concurrency and conflict detection
* [Client Wins](/saving/concurrency-client-wins) — resolve a conflict by keeping the current application values
* [Custom Resolution](/saving/concurrency-custom-resolution) — resolve a conflict by choosing values property by property
* [SaveChanges](/saving/save-changes) — understand when EF Core sends tracked changes to the database

## FAQ

### What does Database Wins mean in EF Core?

Database Wins means the values currently stored in the database are kept.

The current application changes that caused the conflict are discarded, and the entity is refreshed with the latest database values. The application then retries `SaveChangesAsync()` to complete the save operation.

### Does Database Wins save the current changes?

No. Database Wins does not save the current application changes.

It replaces them with the latest database values.

### Why update both CurrentValues and OriginalValues?

`CurrentValues` are updated so the entity in memory matches the database.

`OriginalValues` are updated so EF Core no longer uses the old original concurrency token value when tracking the entity.

### What happens if GetDatabaseValuesAsync returns null?

If `GetDatabaseValuesAsync()` returns `null`, the row no longer exists in the database.

In that case, the entity can be detached so EF Core stops tracking it in the current context.

### When should I use Database Wins instead of another strategy?

Use Database Wins when the latest database values should take priority and the current application changes can be safely discarded.

Use [Client Wins](/saving/concurrency-client-wins) when the current application values should be saved.

Use [Custom Resolution](/saving/concurrency-custom-resolution) when the application needs to compare both versions and choose values property by property.
