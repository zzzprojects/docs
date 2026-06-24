---
title: EF Core Concurrency
description: An overview of how Entity Framework Core detects concurrency conflicts when multiple users or processes modify the same data
canonical: /saving/concurrency
status: Published
lastmod: 2026-06-24
---

# EF Core Concurrency

Concurrency conflicts can happen when two users (or processes) load the same row, change it, and try to save their changes.

EF Core usually handles this with optimistic concurrency by comparing a concurrency token when `SaveChangesAsync()` runs.

With optimistic concurrency, EF Core can:

* compare a concurrency token when saving changes
* detect that a row was changed after it was loaded
* throw `DbUpdateConcurrencyException`
* let the application choose how to resolve the conflict

## Configure a RowVersion Concurrency Token

A concurrency token is a property that EF Core compares when `SaveChangesAsync()` runs.

A common approach, especially with SQL Server, is to use a database-generated `RowVersion` property.

```csharp
public class Product
{
    public int Id { get; set; }
    public string Name { get; set; } = null!;
    public decimal Price { get; set; }

    public byte[] RowVersion { get; set; } = null!;
}
```

You can configure the property as a row version with the Fluent API.

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Product>()
        .Property(p => p.RowVersion)
        .IsRowVersion();
}
```

This tells EF Core to use `RowVersion` as a concurrency token.

In SQL Server, this is commonly mapped to a `rowversion` column that changes automatically whenever the row is updated. The exact database type and behavior can vary depending on the database provider.

## Update an Entity with Optimistic Concurrency

After the concurrency token is configured, you can update the entity normally.

```csharp
using var context = new AppDbContext();

var product = await context.Products.SingleAsync(p => p.Id == 1);

product.Price = 29.99m;

await context.SaveChangesAsync();
```

The code looks like a normal update.

The difference is what EF Core does when `SaveChangesAsync()` runs. EF Core uses the original value of the concurrency token to make sure the row was not changed after it was queried.

For the basic update workflow, see [Updating Data](/saving/modifying-data).

## How EF Core Detects the Conflict

When a concurrency token is configured, EF Core includes the original concurrency token value in the update or delete condition.

Conceptually, the update works like this:

```sql
UPDATE Products
SET Price = @newPrice
WHERE Id = @id
  AND RowVersion = @originalRowVersion;
```

The `Id` identifies the row to update.

The `RowVersion` condition verifies that the row still has the same concurrency token value that EF Core originally loaded.

If no other user or process changed the row, the token still matches and the update succeeds.

If another user or process changed the row first, the token in the database is different. The update does not find a matching row, and the database reports that zero rows were affected.

When EF Core expected to update a row but no row was updated, EF Core treats that as a concurrency conflict and throws `DbUpdateConcurrencyException`.

For more about when EF Core sends changes to the database, see [SaveChanges](/saving/save-changes).

## Catch DbUpdateConcurrencyException

A concurrency conflict should be handled when `SaveChangesAsync()` is called.

```csharp
try
{
    await context.SaveChangesAsync();
}
catch (DbUpdateConcurrencyException)
{
    // The row was changed or deleted after it was loaded.
    // Choose a conflict resolution strategy here.
}
```

Catching the exception is only the first step.

At that point, EF Core has detected the conflict, but it has not decided which values should be saved.

The application must decide whether:

- The database values should win ([Database Wins](#database-wins))
- The current client values should win ([Client Wins](#client-wins))
- The values should be merged with a custom resolution strategy ([Custom Resolution](#custom-resolution))

## Current Values, Original Values, and Database Values

To make that decision, it helps to compare three sets of values involved in a concurrency conflict:

* **Current values** are the values the application tried to save.
* **Original values** are the values EF Core loaded from the database before the entity was changed.
* **Database values** are the values currently stored in the database.

For example:

* Original value: `Price = 25.00`
* Current value: `Price = 29.99`
* Database value: `Price = 27.50`

EF Core can show these values to the application, but it still does not know which value is correct for your business rule.

That decision belongs to the application.

## Read the Database Values

You can use the entries from `DbUpdateConcurrencyException` to inspect the entity that failed to save.

The current database values can be loaded with `GetDatabaseValuesAsync()`.

```csharp
try
{
    await context.SaveChangesAsync();
}
catch (DbUpdateConcurrencyException ex)
{
    foreach (var entry in ex.Entries)
    {
        var databaseValues = await entry.GetDatabaseValuesAsync();

        if (databaseValues == null)
        {
            // The row was deleted by another user or process.
            continue;
        }

        var currentValues = entry.CurrentValues;
        var originalValues = entry.OriginalValues;

        // Compare currentValues, originalValues, and databaseValues
        // before choosing a resolution strategy.
    }
}
```

If `databaseValues` is `null`, the row no longer exists in the database.

If `databaseValues` is not `null`, the row still exists, but at least one concurrency token value no longer matches the original value loaded by EF Core.

At this point, the application can compare:

* the values it tried to save
* the values originally loaded by EF Core
* the values currently stored in the database

Then it can choose a conflict resolution strategy.

## Concurrency Resolution Strategies

This article explains how EF Core detects concurrency conflicts and how the application can decide what should happen next.

Each strategy is covered in detail in its own article.

### Database Wins

Database Wins means the values currently stored in the database are kept.

The current changes from the application are discarded, and the entity is refreshed from the database.

Use this strategy when the latest saved database values should take priority over the current application changes.

For the complete implementation, see [Database Wins](/saving/concurrency-database-wins).

### Client Wins

Client Wins means the current application values are saved even though another user or process changed the row first.

The application refreshes the original concurrency token values and then tries to save again.

This can overwrite changes that were already saved by another user or process, so it should only be used when that behavior matches the business rule.

For the complete implementation, see [Client Wins](/saving/concurrency-client-wins).

### Custom Resolution

Custom Resolution means the application decides which values should be saved property by property.

Some values may come from the database, some values may come from the current user, and some values may be manually selected by a user interface.

This is usually the safest option when different users or processes may have changed different important values.

For the complete implementation, see [Custom Resolution](/saving/concurrency-custom-resolution).

## Other Ways to Configure Concurrency Tokens

`RowVersion` is a common approach, especially when the database can generate a new value automatically every time the row changes.

However, EF Core can also use other properties as concurrency tokens.

For example, you can use a `Guid` property as an application-managed concurrency token.

```csharp
public class Product
{
    public int Id { get; set; }
    public string Name { get; set; } = null!;
    public decimal Price { get; set; }

    public Guid Version { get; set; }
}
```

Then configure the property as a concurrency token.

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Product>()
        .Property(p => p.Version)
        .IsConcurrencyToken();
}
```

Because this value is managed by the application, you must assign a new value when the entity is changed.

```csharp
using var context = new AppDbContext();

var product = await context.Products.SingleAsync(p => p.Id == 1);

product.Price = 29.99m;
product.Version = Guid.NewGuid();

await context.SaveChangesAsync();
```

You can also configure a property as a concurrency token with the `ConcurrencyCheck` attribute.

```csharp
public class Product
{
    public int Id { get; set; }
    public string Name { get; set; } = null!;
    public decimal Price { get; set; }

    [ConcurrencyCheck]
    public Guid Version { get; set; }
}
```

Application-managed tokens can be useful when the database provider does not support an automatically generated row version, or when the application needs more control over which changes should create a new concurrency token.

Be careful when configuring many properties as concurrency tokens. Each token can be included in the update or delete condition, which can make the generated SQL longer.

## Optimistic Locking

Optimistic locking is the usual EF Core approach for concurrency control.

It assumes that conflicts are relatively rare.

Instead of locking the row when it is read, EF Core lets the application make changes normally. The conflict is detected later, when `SaveChangesAsync()` tries to update or delete the row.

This approach works well for many web applications because the application does not need to keep a database transaction open while a user reviews or edits data.

The trade-off is that the application must handle `DbUpdateConcurrencyException` when a conflict is detected.

## Pessimistic Locking

Pessimistic locking tries to prevent conflicts by locking data before another user or process can change it.

This is different from the usual EF Core optimistic concurrency workflow.

EF Core does not provide a single provider-independent API for pessimistic locking that works the same way across all relational databases.

Pessimistic locking usually depends on database-specific behavior, such as:

* transaction isolation levels
* database locks
* raw SQL
* provider-specific locking syntax

This approach can be useful for short-running operations where the application must prevent another transaction from changing the same data.

However, it is usually not a good fit for long user-driven workflows, such as loading a form, waiting for a user to edit values, and then saving later.

Keeping a lock or transaction open during that entire time can reduce concurrency and create blocking problems.

For more about transactions in EF Core, see [Transactions](/saving/transactions).

## Important Behavior

Concurrency handling depends on how the entity and its concurrency tokens are configured.

Important points:

* EF Core uses optimistic concurrency when a concurrency token is configured.
* A concurrency token is loaded and tracked with the entity.
* The original concurrency token value is used when `SaveChangesAsync()` sends an update or delete.
* EF Core checks how many rows were affected by the command.
* If EF Core expected one row to be affected but zero rows were affected, EF Core throws `DbUpdateConcurrencyException`.
* `DbUpdateConcurrencyException` can happen when updating or deleting existing rows.
* Adding a new row usually does not throw `DbUpdateConcurrencyException`.
* A unique constraint violation during insert is usually reported as a provider-specific database exception instead.
* `RowVersion` is commonly used with SQL Server, but database-generated concurrency tokens depend on the provider.
* EF Core detects the conflict, but the application decides how to resolve it.
* Pessimistic locking usually requires database-specific behavior.

For the official EF Core documentation about concurrency conflict handling, see [Handling Concurrency Conflicts](https://learn.microsoft.com/en-us/ef/core/saving/concurrency).

## Common Pitfalls

Be careful with the following mistakes.

### Assuming EF Core Resolves the Conflict Automatically

EF Core can detect that a concurrency conflict happened, but it cannot know which value is correct for your business rule.

```csharp
catch (DbUpdateConcurrencyException)
{
    // EF Core detected the conflict.
    // The application still needs to decide how to resolve it.
}
```

The application must choose a resolution strategy, such as Database Wins, Client Wins, or Custom Resolution.

### Forgetting to Configure a Concurrency Token

EF Core needs a concurrency token to detect whether the row changed after it was loaded.

Without a concurrency token, a normal update can overwrite data without raising a concurrency conflict.

```csharp
public class Product
{
    public int Id { get; set; }
    public string Name { get; set; } = null!;
    public decimal Price { get; set; }
}
```

In this model, there is no concurrency token.

If concurrency conflict detection is required, add a token such as `RowVersion` or configure another property as a concurrency token.

### Treating RowVersion as Provider-Independent

`RowVersion` is commonly used with SQL Server, where it maps to a database-generated `rowversion` column.

Other providers may use different mechanisms, and some providers may not support an automatically generated row version in the same way.

Always check the provider behavior before relying on database-generated concurrency tokens.

### Using Too Many Concurrency Tokens

You can configure multiple properties as concurrency tokens, but that does not always make the model better.

If many large properties are used as concurrency tokens, the update or delete condition can become larger and harder to reason about.

Use concurrency tokens intentionally, based on which changes should actually trigger a conflict.

### Confusing Concurrency with Transactions

Concurrency and transactions solve different problems.

Concurrency handling detects or prevents conflicting changes to the same data.

Transactions control whether a group of operations is committed or rolled back as a unit.

A transaction can be involved in some concurrency scenarios, especially with pessimistic locking or isolation levels, but it does not replace optimistic concurrency conflict handling.

## External Resources - Concurrency

The following videos are useful if you want to see how EF Core concurrency works in practical scenarios.

Together, they cover optimistic concurrency, pessimistic locking, concurrency tokens, `RowVersion`, and `DbUpdateConcurrencyException`.

### Video 1 - Optimistic Locking vs Pessimistic Locking With EF Core

<iframe width="560" height="315" src="https://www.youtube.com/embed/lLDBVMj5He8" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

Milan Jovanović explains the difference between optimistic and pessimistic locking and shows how EF Core can detect conflicting updates with a concurrency token.

The example uses PostgreSQL's `xmin` system column as the concurrency token, so the provider-specific implementation differs from the SQL Server `rowversion` example used in this article. However, the main EF Core idea is the same: EF Core tracks the original concurrency token value, compares it when saving changes, and throws `DbUpdateConcurrencyException` when the expected row is not updated.

This video fits especially well with the optimistic locking and pessimistic locking sections of this article.

Key timestamps:

* [00:00](https://www.youtube.com/watch?v=lLDBVMj5He8&t=0) — Introduction to concurrency conflicts and locking strategies
* [01:30](https://www.youtube.com/watch?v=lLDBVMj5He8&t=90) — Pessimistic locking and why it can affect performance
* [03:00](https://www.youtube.com/watch?v=lLDBVMj5He8&t=180) — Configuring optimistic concurrency with an EF Core concurrency token
* [04:30](https://www.youtube.com/watch?v=lLDBVMj5He8&t=270) — Detecting a conflict and handling `DbUpdateConcurrencyException`

### Video 2 - .NET Data Community Standup: Database Concurrency and EF Core

<iframe width="560" height="315" src="https://www.youtube.com/embed/YfIM-gfJe4c" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

This .NET Data Community Standup provides a deeper discussion of database concurrency and EF Core. It is a longer video, so it is most useful when watched by section rather than from beginning to end.

The most relevant parts for this article cover optimistic concurrency, the difference between optimistic and pessimistic approaches, configuring `RowVersion`, and handling `DbUpdateConcurrencyException`. The video also includes a retry loop section for readers who want to go deeper into conflict handling.

Key timestamps:

* [06:00](https://www.youtube.com/watch?v=YfIM-gfJe4c&t=360) — Introduction to optimistic concurrency and basic concurrency concepts
* [45:00](https://www.youtube.com/watch?v=YfIM-gfJe4c&t=2700) — Difference between optimistic and pessimistic concurrency
* [54:00](https://www.youtube.com/watch?v=YfIM-gfJe4c&t=3240) — Implementing `RowVersion` and handling `DbUpdateConcurrencyException`
* [1:00:00](https://www.youtube.com/watch?v=YfIM-gfJe4c&t=3600) — Retry loop for handling concurrency conflicts

## Summary

EF Core concurrency helps detect when the same row was changed by another user or process before the current changes are saved.

Key points:

* EF Core usually handles concurrency with optimistic concurrency.
* A concurrency token is used to detect whether a row changed after it was loaded.
* `RowVersion` is a common SQL Server approach for database-generated concurrency tokens.
* `SaveChangesAsync()` compares the original concurrency token value when updating or deleting a row.
* If the expected row is not updated, EF Core reports a concurrency conflict with `DbUpdateConcurrencyException`.
* EF Core detects the conflict, but the application decides how to resolve it.
* Common resolution strategies include Database Wins, Client Wins, and Custom Resolution.
* Pessimistic locking is a different approach and usually depends on database-specific behavior.

Use optimistic concurrency when you need to prevent accidental overwrites without locking rows while users edit data.

Use [Database Wins](/saving/concurrency-database-wins) when the latest database values should be kept.

Use [Client Wins](/saving/concurrency-client-wins) when the current user's values should overwrite the database values.

Use [Custom Resolution](/saving/concurrency-custom-resolution) when the application needs to decide property by property which values should be saved.

## Related Articles

If you want to understand how concurrency fits into the EF Core saving workflow, these pages are the best next steps:

* [SaveChanges](/saving/save-changes) — how EF Core sends tracked changes to the database
* [Updating Data](/saving/modifying-data) — how normal entity updates work before concurrency checks are applied
* [Transactions](/saving/transactions) — how EF Core groups operations into a transactional unit of work
* [Connected Entities](/saving/connected-entities) — how EF Core tracks entities that are queried and updated in the same context
* [Database Wins](/saving/concurrency-database-wins) — how to resolve a conflict by keeping the database values
* [Client Wins](/saving/concurrency-client-wins) — how to resolve a conflict by saving the current client values
* [Custom Resolution](/saving/concurrency-custom-resolution) — how to resolve a conflict by choosing values property by property

## FAQ

### What is a concurrency conflict in EF Core?

A concurrency conflict happens when a row was changed or deleted after it was loaded, but before the current changes were saved.

EF Core can detect this when a concurrency token is configured.

### What is a concurrency token in EF Core?

A concurrency token is a property that EF Core compares when `SaveChangesAsync()` runs.

If the value in the database no longer matches the original value loaded by EF Core, the update or delete affects zero rows and EF Core throws `DbUpdateConcurrencyException`.

### What is RowVersion in EF Core?

`RowVersion` is commonly used with SQL Server as a database-generated concurrency token.

The database updates the row version value whenever the row changes, and EF Core uses that value to detect whether the row was modified after it was loaded.

### What does DbUpdateConcurrencyException mean?

`DbUpdateConcurrencyException` means EF Core expected to update or delete a row, but the database reported that no matching row was affected.

This usually means the row was changed or deleted by another user or process after it was loaded.

### Does EF Core resolve concurrency conflicts automatically?

No. EF Core detects the conflict, but the application must decide how to resolve it.

Common strategies include Database Wins, Client Wins, and Custom Resolution.

### What is the difference between optimistic and pessimistic concurrency?

Optimistic concurrency does not lock the row when it is read. Instead, EF Core detects the conflict when changes are saved.

Pessimistic locking tries to prevent conflicts by locking data before another user or process can change it, but this usually depends on database-specific behavior.