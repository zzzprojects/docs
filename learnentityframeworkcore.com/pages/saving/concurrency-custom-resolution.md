---
title: EF Core Concurrency: Custom Resolution
description: How to resolve EF Core concurrency conflicts by choosing values property by property
canonical: /saving/concurrency-custom-resolution
status: Published
lastmod: 2026-06-24
---

# EF Core Concurrency: Custom Resolution

Custom Resolution lets the application decide which values should be saved when a concurrency conflict happens.

## Resolve a Conflict with Custom Resolution

Use `GetDatabaseValuesAsync()` to read the latest database values, compare them with the current values, and choose which values should be saved.

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

            var currentValues = entry.CurrentValues;

            foreach (var property in currentValues.Properties)
            {
                var currentApplicationValue = currentValues[property];
                var databaseValue = databaseValues[property];

                // Keep the application value for Price; use the database value for all other properties.
                if (property.Name != nameof(Product.Price))
                {
                    // Database Wins for all properties different then price
                    currentValues[property] = databaseValues[property];
                }
            }

            entry.OriginalValues.SetValues(databaseValues);
        }
    }
}
```

In this example:

* `GetDatabaseValuesAsync()` reads the latest values from the database.
* `CurrentValues` contains the values the application tried to save.
* `OriginalValues` contains the values originally loaded by EF Core.
* The values returned by `GetDatabaseValuesAsync()` are the values currently stored in the database.
* The application chooses which value to keep for each property.
* `OriginalValues.SetValues(databaseValues)` refreshes the original values before retrying `SaveChangesAsync()`.

In this example, the application keeps the current `Price` value and uses the database value for every other property.

The important difference from Database Wins and Client Wins is that Custom Resolution does not choose one entire version.

Instead, the application decides property by property.

## Example Scenario

Assume a product is loaded with these values:

```text
Name = "Phone"
Price = 25.00
```

The application changes the price:

```text
Name = "Phone"
Price = 29.99
```

Before the application saves, another user or process changes the name in the database:

```text
Name = "Smartphone"
Price = 25.00
```

When `SaveChangesAsync()` runs, EF Core detects the concurrency conflict and throws `DbUpdateConcurrencyException`.

With Custom Resolution, the application can choose the final values property by property:

```text
Name = "Smartphone"
Price = 29.99
```

In this scenario:

* `Name` comes from the database.
* `Price` comes from the current application values.

After the original values are refreshed and `SaveChangesAsync()` is retried, EF Core saves the resolved values.

## How Custom Resolution Works

Custom Resolution works by comparing the values involved in the concurrency conflict and deciding what the final current values should be.

The application can inspect:

* `entry.CurrentValues`
* `entry.OriginalValues`
* `entry.GetDatabaseValuesAsync()`

The final values to save must be assigned to `CurrentValues` if modified.

```csharp
currentValues[property] = databaseValue;
```


After the final values are chosen, refresh the original values with the latest database values.

```csharp
entry.OriginalValues.SetValues(databaseValues);
```

This tells EF Core to retry the update using the latest original concurrency token values.

If no new conflict happens, the resolved current values are saved.

## When to Use Custom Resolution

Use Custom Resolution when the application must decide the final values property by property.

This strategy is useful when neither Database Wins nor Client Wins is enough.

Common examples include:

* different users changed different important fields
* some values should come from the database and some values should come from the current application
* the user should review the conflict before saving
* business rules decide which value has priority

Custom Resolution is usually the best fit when the conflict cannot be resolved by choosing one entire version.

## When Not to Use Custom Resolution

Do not use Custom Resolution when the application can safely choose one complete version without comparing values property by property.

For example, Custom Resolution is usually not necessary when:

* the latest database values should always win
* the current application values should always overwrite the database values
* the conflict can be resolved without comparing values property by property
* the application does not need user input or business-specific merge rules

In those cases, use [Database Wins](/saving/concurrency-database-wins) or [Client Wins](/saving/concurrency-client-wins).

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

If the current operation is a delete, the operation can often be considered successful.

Otherwise, in this example, the exception stops the retry because there are no database values to compare.

In a Custom Resolution strategy, the application usually cannot merge values when the row was deleted.

At that point, the application must decide whether to:

* stop the operation
* show an error to the user
* recreate the row with a new insert flow

Do not continue with property-by-property resolution when the row no longer exists.

## Important Behavior

Custom Resolution lets the application choose the final values to save.

Important points:

* `GetDatabaseValuesAsync()` reads the latest values from the database.
* The final values must be assigned to `CurrentValues`.
* `OriginalValues.SetValues(databaseValues)` refreshes the values EF Core uses for concurrency checking.
* `SaveChangesAsync()` must be retried after the final values are chosen.
* If the retry succeeds, the resolved current values are saved.
* If another conflict happens before the retry succeeds, `DbUpdateConcurrencyException` can be thrown again.
* If `databaseValues` is `null`, the row was deleted by another user or process.
* EF Core does not automatically merge values.
* Custom Resolution does not decide which value is correct for the application.

For a broader overview of concurrency conflict handling, see [Concurrency](/saving/concurrency).

## Common Pitfalls

Be careful with the following mistakes.

### Expecting EF Core to Merge Values Automatically

EF Core can detect the concurrency conflict, but it cannot decide which value is correct for the application.

```csharp
var currentApplicationValue = currentValues[property];
var databaseValue = databaseValues[property];
```

The application must decide which value should be assigned to `CurrentValues` for each property.

If the application always wants the database values, use [Database Wins](/saving/concurrency-database-wins).

If the application always wants the current values, use [Client Wins](/saving/concurrency-client-wins).

### Forgetting to Assign the Final Values to CurrentValues

When `SaveChangesAsync()` is retried, EF Core saves the values currently assigned to `CurrentValues`.

```csharp
currentValues[property] = databaseValue;
````

If the resolved value comes from the database, the application must assign it to `CurrentValues`.

Reading the database values is not enough.

The application must assign the resolved values to `CurrentValues` before retrying `SaveChangesAsync()`.

### Forgetting to Refresh OriginalValues

After choosing the final values, refresh the original values with the latest database values.

```csharp
entry.OriginalValues.SetValues(databaseValues);
```

If `OriginalValues` are not refreshed, EF Core may retry the update with the old original concurrency token value and throw another `DbUpdateConcurrencyException`.

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

Do not continue with property-by-property resolution when `databaseValues` is `null`.

## Summary

Custom Resolution lets the application choose which values should be saved when a concurrency conflict happens.

Use this strategy when neither the full database version nor the full current application version should automatically win.

Key points:

* `GetDatabaseValuesAsync()` reads the latest values from the database.
* The final values must be assigned to `CurrentValues`.
* `OriginalValues` must be refreshed before the retry.
* `SaveChangesAsync()` sends the resolved `CurrentValues` when it is retried.
* If `GetDatabaseValuesAsync()` returns `null`, the row no longer exists in the database.

EF Core detects the conflict and exposes the values involved, but the application decides which values should be saved.

Use Database Wins when all database values should be kept.

Use Client Wins when all current application values should overwrite the database values.

Use Custom Resolution when the application needs to compare both versions and choose values property by property.

## Related Articles

If you want to continue with EF Core concurrency conflict handling, these articles are the best next steps:

* [Concurrency](/saving/concurrency) — overview of EF Core concurrency and conflict detection
* [Database Wins](/saving/concurrency-database-wins) — resolve a conflict by keeping the database values
* [Client Wins](/saving/concurrency-client-wins) — resolve a conflict by keeping the current application values
* [SaveChanges](/saving/save-changes) — understand when EF Core sends tracked changes to the database

## FAQ

### What does Custom Resolution mean in EF Core?

Custom Resolution means the application chooses which values should be saved when a concurrency conflict happens.

Instead of keeping the full database version or the full current application version, the application chooses the final values property by property.

### Does EF Core automatically merge concurrency conflicts?

No. EF Core detects the conflict and exposes the current values, original values, and database values.

The application must decide which values should be saved.

### Where should the final resolved values be assigned?

The final resolved values must be assigned to `CurrentValues`.

When `SaveChangesAsync()` is retried, EF Core saves the values currently stored in `CurrentValues`.

### Why refresh OriginalValues after choosing the final values?

`OriginalValues` are refreshed so EF Core can retry the update using the latest concurrency token values from the database.

Without refreshing `OriginalValues`, the retry may use the old original concurrency token value and fail again.

### What happens if GetDatabaseValuesAsync returns null?

If `GetDatabaseValuesAsync()` returns `null`, the row no longer exists in the database.

If the current operation is a delete, the operation can often be considered successful.

Otherwise, the application cannot continue with property-by-property resolution for that row.

### When should I use Custom Resolution instead of Database Wins or Client Wins?

Use Custom Resolution when the application needs to compare both versions and choose values property by property.

Use [Database Wins](/saving/concurrency-database-wins) when the database values should be kept.

Use [Client Wins](/saving/concurrency-client-wins) when the current application values should overwrite the database values.

