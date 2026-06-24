---
title: EF Core Concurrency: Client Wins
description: How to resolve EF Core concurrency conflicts by keeping the current application values and overwriting the database values
canonical: /saving/concurrency-client-wins
status: Published
lastmod: 2026-06-24
---

# EF Core Concurrency: Client Wins

Client Wins keeps the current application values by refreshing the original values and retrying `SaveChangesAsync()`.

## Resolve a Conflict with Client Wins

Refresh the original values with the latest database values, then retry `SaveChangesAsync()`.

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
                if (entry.State == EntityState.Deleted)
                {
                    // Only do this if an already deleted row should be treated as a successful delete.
                    entry.State = EntityState.Detached;
                    continue;
                }
				
                throw new InvalidOperationException("The row was deleted by another user or process.");
            }

            entry.OriginalValues.SetValues(databaseValues);
        }
    }
}
```

In this example:

* The current application values are kept.
* `GetDatabaseValuesAsync()` reads the latest database values.
* `OriginalValues.SetValues(databaseValues)` refreshes the original values tracked by EF Core.
* All original values are refreshed, not only the concurrency token. This ensures the next `UPDATE` statement is generated using the latest database values.
* `SaveChangesAsync()` is retried after the original values are refreshed.

The important difference from Database Wins is that `CurrentValues` are not replaced.

The application keeps the values it tried to save and retries the update using the latest database values. This allows the update to succeed while preserving the application changes.

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

When `SaveChangesAsync()` runs, EF Core detects the concurrency conflict and throws `DbUpdateConcurrencyException`.

With Client Wins, the application keeps its current value:

```text
Price = 29.99
```

After the original values are refreshed and `SaveChangesAsync()` is retried, the database value is overwritten.

The final value saved to the database is:

```text
Price = 29.99
```

## How Client Wins Works

Client Wins works by keeping the current application values and refreshing only the original values used for concurrency checking.

```csharp
entry.OriginalValues.SetValues(databaseValues);
```

The current values remain unchanged.

The original values are refreshed so EF Core no longer compares against the old original concurrency token value.

When `SaveChangesAsync()` runs again, EF Core uses the refreshed original values in the update condition.

If no new conflict happens, the update succeeds and the current application values are saved.

This means Client Wins can overwrite changes that were already saved by another user or process.

## When to Use Client Wins

Use Client Wins when the current application values can safely overwrite the latest database values.

This strategy is useful when the current change has priority and can be safely saved again after the concurrency conflict is detected.

Common examples include:

* administrative corrections
* automated updates where the application is the source of truth
* workflows where the current operation should replace previous saved changes
* simple fields where overwriting the latest database value is acceptable

Client Wins is usually a good fit when the application already knows that the current values should be saved, even if another user or process saved changes first.

## When Not to Use Client Wins

Do not use Client Wins when overwriting the latest database values could lose important data.

Use another strategy when the application needs to compare both versions before deciding.

For example, Client Wins is usually not the best choice when:

* another user or process may have changed important fields
* both versions contain useful information
* the user should review the conflict before saving
* different properties should be resolved differently

In those cases, use [Database Wins](/saving/concurrency-database-wins) or [Custom Resolution](/saving/concurrency-custom-resolution).

## Handle Deleted Rows

If `GetDatabaseValuesAsync()` returns `null`, the row no longer exists in the database.

```csharp
var databaseValues = await entry.GetDatabaseValuesAsync();

if (databaseValues == null)
{
    if (entry.State == EntityState.Deleted)
    {
        // Only do this if an already deleted row should be treated as a successful delete.
        entry.State = EntityState.Detached;
        continue;
    }
	
    throw new InvalidOperationException("The row was deleted by another user or process.");
}
```

If the conflicting operation is a delete and the row has already been deleted by another user or process, the operation can often be considered successful.

However, for updates, there are no database values available to refresh the original values, so the Client Wins retry cannot continue.

At that point, the application must decide whether to:

* stop the operation
* show an error to the user
* recreate the row with a new insert flow
* use another business-specific recovery flow

Do not retry the same update as Client Wins when the row no longer exists.

## Important Behavior

Client Wins keeps the current application values.

Important points:

* `GetDatabaseValuesAsync()` reads the latest values from the database.
* `OriginalValues.SetValues(databaseValues)` refreshes the values EF Core uses for concurrency checking.
* `CurrentValues` are not replaced.
* `SaveChangesAsync()` is retried after the original values are refreshed.
* If the retry succeeds, the current application values overwrite the latest database values.
* If another conflict happens before the retry succeeds, `DbUpdateConcurrencyException` can be thrown again.
* If `databaseValues` is `null`, the row was deleted by another user or process.
* Client Wins does not merge values property by property.

For a broader overview of concurrency conflict handling, see [Concurrency](/saving/concurrency).

## Common Pitfalls

Be careful with the following mistakes.

### Expecting Client Wins to Merge Values

Client Wins does not merge values from both versions.

```csharp
entry.OriginalValues.SetValues(databaseValues);
```

This refreshes the original values used for concurrency checking.

It does not copy database values into the current entity values.

The current application values remain the values that will be saved.

If the application needs to choose values property by property, use [Custom Resolution](/saving/concurrency-custom-resolution).

### Forgetting to Retry SaveChangesAsync

Refreshing `OriginalValues` does not save the changes by itself.

```csharp
entry.OriginalValues.SetValues(databaseValues);
```

After the original values are refreshed, the application must call `SaveChangesAsync()` again.

That is why Client Wins is commonly implemented inside a retry loop.

### Ignoring Deleted Rows

`GetDatabaseValuesAsync()` can return `null`.

```csharp
if (databaseValues == null)
{
    if (entry.State == EntityState.Deleted)
    {
        // Only do this if an already deleted row should be treated as a successful delete.
        entry.State = EntityState.Detached;
        continue;
    }
	
    throw new InvalidOperationException("The row was deleted by another user or process.");
}
```

This means the row was deleted before the current changes were saved.

If the current operation is a delete, the operation can often be considered successful.

Do not call `OriginalValues.SetValues(databaseValues)` when `databaseValues` is `null`.

### Using Client Wins When Data Loss Matters

Client Wins can overwrite changes that were already saved by another user or process.

Use it only when that behavior is acceptable.

If overwriting the latest database values could lose important data, use [Database Wins](/saving/concurrency-database-wins) or [Custom Resolution](/saving/concurrency-custom-resolution).

## Summary

Client Wins keeps the current application values by refreshing the original values and retrying `SaveChangesAsync()`.

Use this strategy when the current application values can safely overwrite the latest database values.

Key points:

* `GetDatabaseValuesAsync()` reads the latest values from the database.
* `OriginalValues` are refreshed so EF Core no longer uses the old original concurrency token value.
* `CurrentValues` are not replaced.
* `SaveChangesAsync()` must be retried after the original values are refreshed.
* If `GetDatabaseValuesAsync()` returns `null`, the row no longer exists in the database.

The main risk is that Client Wins can overwrite changes already saved by another user or process.

Use another strategy when the current changes must be reviewed, merged, or compared before saving.

## Related Articles

If you want to continue with EF Core concurrency conflict handling, these articles are the best next steps:

* [Concurrency](/saving/concurrency) — overview of EF Core concurrency and conflict detection
* [Database Wins](/saving/concurrency-database-wins) — resolve a conflict by keeping the database values
* [Custom Resolution](/saving/concurrency-custom-resolution) — resolve a conflict by choosing values property by property
* [SaveChanges](/saving/save-changes) — understand when EF Core sends tracked changes to the database

## FAQ

### What does Client Wins mean in EF Core?

Client Wins means the current application values are kept and saved if the retry succeeds.

EF Core refreshes the original values used for concurrency checking, then the application retries `SaveChangesAsync()`.

### Does Client Wins overwrite database values?

Yes. If the retry succeeds, Client Wins can overwrite values that were already saved by another user or process.

Use this strategy only when overwriting those values is acceptable.

### Why update OriginalValues but not CurrentValues?

`OriginalValues` are updated so EF Core can retry the update using the latest concurrency token values.

`CurrentValues` are not replaced because they are the values the application wants to save.

### What happens if GetDatabaseValuesAsync returns null?

If `GetDatabaseValuesAsync()` returns `null`, the row no longer exists in the database.

If the current operation is a delete, the operation can often be considered successful.

Otherwise, Client Wins cannot simply refresh the original values and retry the same update.

### When should I use Client Wins instead of another strategy?

Use Client Wins when the current application values should overwrite the latest database values.

Use [Database Wins](/saving/concurrency-database-wins) when the database values should be kept.

Use [Custom Resolution](/saving/concurrency-custom-resolution) when the application needs to compare both versions and choose values property by property.