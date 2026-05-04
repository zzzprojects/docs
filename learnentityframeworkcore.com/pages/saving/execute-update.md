---
title: EF Core ExecuteUpdate (EF Core 7–10) – Set-Based Bulk Updates
description: Learn how EF Core ExecuteUpdate works from EF Core 7 to EF Core 10. Perform fast, set-based SQL updates without tracking, plus new JSON and lambda features.
canonical: /saving/execute-update
status: Published
lastmod: 2026-05-04
---

# EF Core ExecuteUpdate

`ExecuteUpdateAsync()` updates rows directly in the database without loading entities into memory, without tracking them, and without calling [`SaveChangesAsync()`](/saving/save-changes).

It is useful when you want to update one or more property (column) for all rows that match a query.

## Update Rows with a Filter

Most of the time, you will combine `Where()` with `ExecuteUpdateAsync()`.

```csharp
using var context = new AppDbContext();

await context.Blogs
    .Where(b => b.Rating < 3)
    .ExecuteUpdateAsync(s => s
        .SetProperty(b => b.IsVisible, false));
```

Executed SQL:

```sql
UPDATE [b]
    SET [b].[IsVisible] = CAST(0 AS bit)
FROM [Blogs] AS [b]
WHERE [b].[Rating] < 3
```

Only rows that match the filter are updated.

## Update All Rows

If you call `ExecuteUpdateAsync()` directly from a `DbSet`, EF Core updates every row in that table.

```csharp
using var context = new AppDbContext();

await context.Blogs
    .ExecuteUpdateAsync(s => s
        .SetProperty(b => b.IsVisible, true));
```

This executes a database `UPDATE` for all `Blogs`.

```sql
UPDATE [b]
    SET [b].[IsVisible] = CAST(0 AS bit)
FROM [Blogs] AS [b]
```

Use this carefully because no filter is applied.

## Update One Property

Use `SetProperty()` to specify the property you want to update.

```csharp
using var context = new AppDbContext();

await context.Blogs
    .Where(b => b.Name == "ZZZ Projects")
    .ExecuteUpdateAsync(s => s
        .SetProperty(b => b.Rating, 5));
```

This updates only the `Rating` property for the matching blog.

## Update Multiple Properties

You can chain multiple `SetProperty()` calls to update more than one property.

```csharp
using var context = new AppDbContext();

await context.Blogs
    .Where(b => b.Rating < 3)
    .ExecuteUpdateAsync(s => s
        .SetProperty(b => b.Rating, 3)
        .SetProperty(b => b.IsVisible, false));
```

This updates both `Rating` and `IsVisible` in the same database command.

## Update Using the Current Value

The new value can also be based on the current database value.

```csharp
using var context = new AppDbContext();

await context.Blogs
    .Where(b => b.IsVisible)
    .ExecuteUpdateAsync(s => s
        .SetProperty(b => b.Rating, blog => blog.Rating + 1));
```

This increments the current `Rating` value for all visible blogs.

The expression must be translatable to SQL.

## Update with a Navigation Filter

You can use navigation data in the query filter before calling `ExecuteUpdateAsync()`.

```csharp
using var context = new AppDbContext();

await context.Blogs
	.Where(b => b.Posts.All(p => p.PublishedOn.Year < 2018))
	.ExecuteUpdateAsync(s => s
		.SetProperty(post => post.IsVisible, false));
```

This updates all blogs which contains only posts published before 2018.

The filter is translated to SQL and executed directly in the database.

## Update using a regular lambda

Starting in [EF Core 10](https://learn.microsoft.com/en-us/ef/core/what-is-new/ef-core-10.0/whatsnew#executeupdateasync-now-accepts-a-regular-non-expression-lambda), `ExecuteUpdateAsync` accepts a **regular lambda**, not only expression trees.

This makes dynamic/conditional updates easier to write, as long as every `SetProperty` remains **SQL-translatable**.

```csharp
bool nameChanged = true; // Local variable (not a column)
await context.Blogs
    .Where(b => b.Rating < 3)
    .ExecuteUpdateAsync(s =>
{
    s.SetProperty(b => b.Views, 8);
    if (nameChanged)
    {
        s.SetProperty(b => b.Name, "foo");
    }
});
```

## Rows Affected

`ExecuteUpdateAsync()` returns an `int` value that indicates the number of rows updated.

```csharp
using var context = new AppDbContext();

var rowsAffected = await context.Blogs
    .Where(b => b.Rating < 3)
    .ExecuteUpdateAsync(s => s
        .SetProperty(b => b.IsVisible, false));
```

You can use this value for logging, validation, or checking whether the update matched the expected rows.

## Important Behavior

`ExecuteUpdateAsync()` executes immediately.

That means:

- it does not require for `SaveChangesAsync()`
- it does not use the `ChangeTracker`
- it does not update tracked entity instances already loaded in memory
- it sends an `UPDATE` command directly to the database

For tracked entity workflows, see [SaveChanges](/saving/save-changes).

For a deeper explanation of tracking behavior, see [ChangeTracker](/saving/change-tracker).

## ExecuteUpdate Requirements

`ExecuteUpdateAsync()` is available starting with EF Core 7.

_(For older versions of EF Core (EF Core 2+) or if you prefer the syntax provided by EF Extensions over using SetProperty, you can use **[UpdateFromQuery](https://entityframework-extensions.net/update-from-query)**.)_

To use it correctly, keep these requirements in mind:

- the update must be based on an EF Core query
- the values passed to `SetProperty()` must be translatable to SQL
- the operation runs immediately in the database
- tracked entities already loaded in the current `DbContext` are not automatically updated in memory
- `SaveChangesAsync()` is not required after calling `ExecuteUpdateAsync()`

For example, this works because the expression can be translated to SQL:

```csharp
using var context = new AppDbContext();

await context.Blogs
    .Where(b => b.Rating < 5)
    .ExecuteUpdateAsync(s => s
        .SetProperty(b => b.Rating, b => b.Rating + 1));
```

This increments the `Rating` column directly in the database.

However, custom .NET methods that cannot be translated to SQL cannot be used inside `SetProperty()`.

```csharp
using var context = new AppDbContext();

await context.Blogs
    .ExecuteUpdateAsync(s => s
        .SetProperty(b => b.Name, b => NormalizeName(b.Name)));
```

This kind of expression cannot be translated unless `NormalizeName()` is mapped or translated by the provider.

## How ExecuteUpdate Works

`ExecuteUpdateAsync()` sends an `UPDATE` command directly to the database.

It does not follow the normal tracked entity workflow.

A normal tracked update usually looks like this:

```csharp
using var context = new AppDbContext();

var blog = await context.Blogs
    .FirstAsync(b => b.Name == "ZZZ Projects");

blog.Rating = 5;

await context.SaveChangesAsync();
```

In that workflow, EF Core:

1. loads the entity
2. tracks it in the `DbContext`
3. detects the property change
4. generates the update when `SaveChangesAsync()` runs

`ExecuteUpdateAsync()` skips that workflow.

```csharp
using var context = new AppDbContext();

await context.Blogs
    .Where(b => b.Name == "ZZZ Projects")
    .ExecuteUpdateAsync(s => s
        .SetProperty(b => b.Rating, 5));
```

In this case, EF Core translates the query and update expression into a direct database command.

No entity instance is loaded, and the `ChangeTracker` is not involved.

## ExecuteUpdate vs SaveChanges

`ExecuteUpdateAsync()` and `SaveChangesAsync()` both update data, but they are designed for different workflows.

| Feature | `ExecuteUpdateAsync()` | `SaveChangesAsync()` |
|---|---|---|
| Loads entities | No | Usually yes |
| Uses ChangeTracker | No | Yes |
| Requires `SaveChangesAsync()` | No | It is the save operation |
| Best for | Set-based updates | Tracked entity workflows |
| Updates in-memory tracked entities | No | Yes |
| Can use per-entity business logic in memory | No | Yes |

Use `ExecuteUpdateAsync()` when you want to update rows directly in the database.

Use `SaveChangesAsync()` when you are working with tracked entities and want EF Core to detect changes during a unit of work.

For the tracked entity workflow, see [SaveChanges](/saving/save-changes).

For tracking behavior, see [ChangeTracker](/saving/change-tracker).

## ExecuteUpdate vs BulkUpdate

Is `ExecuteUpdate` a real “Bulk Update”? Not quite. Yes, it update rows in bulk directly in SQL. However, it **does not** bulk update entities with their own per-row values like [BulkUpdate](https://entityframework-extensions.net/bulk-update) (Entity Framework Extensions).

`ExecuteUpdateAsync()` is a set-based update API built into EF Core.

It is useful when every matching row can be updated using the same SQL-translatable rule.

```csharp
using var context = new AppDbContext();

await context.Blogs
    .Where(b => b.Rating < 3)
    .ExecuteUpdateAsync(s => s
        .SetProperty(b => b.IsVisible, false));
```

This works well because the update is expressed as one database rule.

However, `ExecuteUpdateAsync()` is not the same as a full bulk update library.

Use [BulkUpdate](https://entityframework-extensions.net/bulk-update) from Entity Framework Extensions when:

- each row has different values coming from memory
- you need to update many entities from a list
- you need advanced options such as batching, auditing, custom mappings, or output values
- you need more control over large data synchronization scenarios

In short:

- use `ExecuteUpdateAsync()` for set-based updates expressed as a query
- use `BulkUpdate` when each row needs different values from entities in memory

| Question | `ExecuteUpdateAsync()` | [BulkUpdate (EF Extensions)](https://entityframework-extensions.net/bulk-update) |
|---|---|---|
| How are values applied? | One SQL rule for all matching rows | Values come from each entity |
| Where does the data come from? | Query expression translated to SQL | In-memory entity list |
| Different values per row? | No | Yes |
| Uses ChangeTracker? | No | Not the normal EF Core tracking workflow |
| Best for | Set-based updates | High-volume updates with per-row values |

### Performance Benchmarks

Updating **100,000 rows** (3 `int` columns + 3 `string` columns):

| Method | Time | Memory |
|---|---:|---|
| `ExecuteUpdate` | 365 ms | Very low |
| `BulkUpdate` (EF Extensions) | 1900 ms | Low |
| `SaveChanges` | 4800 ms | High |

[Benchmark Source](https://github.com/zzzprojects/learnentityframeworkcore/blob/main/benchmarks/EFCore/EFCore.Benchmarks/Benchmarks/ExecuteUpdateVsSaveChanges.cs)

> **Important:** For `BulkUpdate` and `SaveChanges`, part of the time is spent materializing the list of entities from the database before the update starts. This step is required because `BulkUpdate` and `SaveChanges` work from entities, not from a LINQ rule.

**Takeaway:** `ExecuteUpdate` can be much faster than the traditional tracked approach when the update can be expressed as a set-based SQL rule. `BulkUpdate` remains more appropriate when each row needs different values coming from entities in memory.

### Bulk Update - Concrete Example

`ExecuteUpdateAsync()` works well when the same SQL rule applies to every matching row.

A real [bulk update](https://entityframework-extensions.net/bulk-update) from EF Extensions is different: each entity can carry its own value from memory.

For example, imagine you need to update 1,000,000 products, and each product already has a different `NewPrice` value calculated in memory.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

var products = GetMillionProductsWithNewPrices();

context.BulkUpdate(products, options =>
{
    options.ColumnInputExpression = p => new { p.Id, p.NewPrice };
    options.BatchSize = 10000;
});
```

Doing the same with `ExecuteUpdateAsync()` in bulk is impossible.

`ExecuteUpdateAsync()` and `BulkUpdate` complement each other rather than compete.

## When to Use ExecuteUpdate

Use `ExecuteUpdateAsync()` when:

- you want to update rows directly in the database
- the update can be expressed as a SQL-translatable rule
- you do not need to load entities first
- you do not need the `ChangeTracker`
- you do not need entity instances updated in memory
- you want to update many rows with one database command

Good examples include:

- hiding old records
- increasing a numeric value
- setting a status for all rows that match a filter
- updating a flag based on related data
- applying a simple batch correction

```csharp
using var context = new AppDbContext();

await context.Blogs
    .Where(b => b.LastUpdated < cutoffDate)
    .ExecuteUpdateAsync(s => s
        .SetProperty(b => b.IsArchived, true));
```

## When Not to Use ExecuteUpdate

`ExecuteUpdateAsync()` is not the best choice when the update depends on tracked entity behavior.

Avoid it when:

- you need to update already loaded entity instances in memory
- you rely on `ChangeTracker` state
- you need entity events or in-memory business logic before saving
- each row needs a different value from a local object list
- the update expression cannot be translated to SQL
- you need the normal `SaveChangesAsync()` pipeline

For example, if your application loads an entity, applies domain logic, validates it, and then saves it, a tracked workflow is usually clearer.

```csharp
using var context = new AppDbContext();

var blog = await context.Blogs
    .FirstAsync(b => b.Name == "ZZZ Projects");

blog.Rating = 5;

await context.SaveChangesAsync();
```

For normal tracked updates, see [Updating Data](/saving/modifying-data).

## Common Pitfalls

Be careful with the following mistakes.

### Expecting SaveChangesAsync to Be Required

`ExecuteUpdateAsync()` runs immediately.

This is unnecessary:

```csharp
using var context = new AppDbContext();

await context.Blogs
    .Where(b => b.Rating < 3)
    .ExecuteUpdateAsync(s => s
        .SetProperty(b => b.IsVisible, false));

// DO NOT USE!
await context.SaveChangesAsync();
```

The `SaveChangesAsync()` call does not save the `ExecuteUpdateAsync()` operation. The update already happened.

### Expecting Tracked Entities to Be Updated in Memory

If an entity is already tracked, `ExecuteUpdateAsync()` does not automatically update that in-memory instance.

```csharp
using var context = new AppDbContext();

var blog = await context.Blogs
    .FirstAsync(blog => blog.Name == "ZZZ Projects");

await context.Blogs
    .Where(b => b.Name == "ZZZ Projects")
    .ExecuteUpdateAsync(s => s
        .SetProperty(b => b.Rating, 5));

// The tracked blog instance may still have the old Rating value.
```

If you need fresh values, reload the entity or use a new `DbContext`.

### Using Non-Translatable Expressions

The update expression must be translatable to SQL.

```csharp
using var context = new AppDbContext();

await context.Blogs
    .ExecuteUpdateAsync(s => s
        .SetProperty(b => b.Name, b => NormalizeName(b.Name)));
```

This fails unless the method can be translated by the provider.

Keep `SetProperty()` expressions simple and SQL-translatable.

## ExecuteUpdate Release History

* **EF Core 10:**
  * [JSON support](https://learn.microsoft.com/en-us/ef/core/what-is-new/ef-core-10.0/whatsnew#executeupdate-support-for-relational-json-columns): Added `ExecuteUpdate` support for relational JSON columns, allowing efficient bulk updates of JSON properties when mapped as complex types.
  * [Easier dynamic updates](https://learn.microsoft.com/en-us/ef/core/what-is-new/ef-core-10.0/whatsnew#executeupdateasync-now-accepts-a-regular-non-expression-lambda): `ExecuteUpdateAsync` now accepts a regular lambda (not only expression trees), making dynamic and conditional updates much easier to write.
* [EF Core 9](https://learn.microsoft.com/en-us/ef/core/what-is-new/ef-core-9.0/whatsnew#executeupdate): Improved `ExecuteUpdate` to support complex type properties.
* [EF Core 8](https://learn.microsoft.com/en-us/ef/core/what-is-new/ef-core-8.0/whatsnew#better-executeupdate-and-executedelete): Improved `ExecuteUpdate` and `ExecuteDelete` to support more complex queries (owned types, unions, and TPT), as long as all updates target a single database table.
* [EF Core 7](https://learn.microsoft.com/en-us/ef/core/what-is-new/ef-core-7.0/whatsnew#executeupdate-and-executedelete-bulk-updates): Introduced `ExecuteUpdate` and `ExecuteDelete`.
* **EF Core 2+:** For older versions of EF Core or if you prefer the syntax provided by EF Extensions over using `SetProperty`, you can use [UpdateFromQuery](https://entityframework-extensions.net/update-from-query).

`ExecuteUpdateAsync()` was introduced in EF Core 7.

The main idea is the same across EF Core 7, 8, 9, and 10: update matching rows directly in the database without loading entities.

However, newer EF Core versions may improve translation support, provider behavior, and supported query shapes.

The exact behavior can still depend on the database provider.

## External Resources - ExecuteUpdate

The following videos are useful if you want to see `ExecuteUpdateAsync()` in real EF Core examples, especially how it differs from tracked updates with `SaveChangesAsync()` and how it avoids unnecessary entity loading.

### Video 1 - EF Core 7 - Performance Improvements With ExecuteUpdate and ExecuteDelete

<iframe width="560" height="315" src="https://www.youtube.com/embed/VYitXAc_htI?si=zfdIx_3RPKQN7WXu" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

Milan Jovanović demonstrates how to replace a slow foreach-based update with `ExecuteUpdate`. He applies a computed value, explains how the update runs directly in the database without tracking entities, and compares performance against the traditional approach.

Key timestamps:

- [1:45](https://www.youtube.com/watch?v=VYitXAc_htI&t=105) — Creating the new endpoint based on `ExecuteUpdate`
- [2:30](https://www.youtube.com/watch?v=VYitXAc_htI&t=150) — Filtering employees by `CompanyId` using LINQ
- [3:00](https://www.youtube.com/watch?v=VYitXAc_htI&t=180) — Applying a percentage salary increase with `SetProperty`
- [3:45](https://www.youtube.com/watch?v=VYitXAc_htI&t=225) — No tracking and no `SaveChanges` call
- [4:45](https://www.youtube.com/watch?v=VYitXAc_htI&t=285) — SQL `UPDATE` generated by EF Core 7
- [6:00](https://www.youtube.com/watch?v=VYitXAc_htI&t=360) — Performance comparison: foreach update vs. `ExecuteUpdate`

### Video 2 - Entity Framework 7 Makes Performing Updates and Deletes Easy

<iframe width="560" height="315" src="https://www.youtube.com/embed/yDFwJ7JfG3s?si=-xRNkhwd9IhRX6pm" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

Israel Quiroz explains how `ExecuteUpdateAsync()` replaces the traditional workflow of retrieving an entity, modifying properties, and calling `SaveChangesAsync()`. He shows how a single database call can update several fields using `SetProperty`, without tracking entities.

Key timestamps:

- [3:28](https://www.youtube.com/watch?v=yDFwJ7JfG3s&t=208) — Traditional update workflow: retrieve, assign, `SaveChanges`
- [3:40](https://www.youtube.com/watch?v=yDFwJ7JfG3s&t=220) — `ExecuteUpdateAsync` with `SetProperty`
- [4:00](https://www.youtube.com/watch?v=yDFwJ7JfG3s&t=240) — Chained `SetProperty` calls
- [4:20](https://www.youtube.com/watch?v=yDFwJ7JfG3s&t=260) — Fewer database round-trips
- [4:40](https://www.youtube.com/watch?v=yDFwJ7JfG3s&t=280) — No change tracking during the update

### Video 3 - Spanish: Borrado y Actualizaciones Masivas - Nuevo de EF Core 7

<iframe width="560" height="315" src="https://www.youtube.com/embed/o9B3pxVpPIY?si=z2N0sPCUOE9xBEqp" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

Felipe Gavilan demonstrates mass updates using `ExecuteUpdate` with multiple `SetProperty` calls. He updates a timestamp and a string field, verifies the results in SQL Server, and shows how all changes are performed in one consolidated SQL `UPDATE`.

Key timestamps:

- [5:32](https://www.youtube.com/watch?v=o9B3pxVpPIY&t=332) — First `SetProperty`: assigning the update timestamp
- [6:48](https://www.youtube.com/watch?v=o9B3pxVpPIY&t=408) — Second `SetProperty`: appending text
- [7:48](https://www.youtube.com/watch?v=o9B3pxVpPIY&t=468) — Running the mass update and reviewing results
- [8:31](https://www.youtube.com/watch?v=o9B3pxVpPIY&t=511) — Beginning of performance comparison
- [9:31](https://www.youtube.com/watch?v=o9B3pxVpPIY&t=571) — Final numbers: 100k updates

## Summary

`ExecuteUpdateAsync()` lets you update rows directly in the database without loading entities, tracking them, or calling `SaveChangesAsync()`.

Key points:

- it executes immediately
- it works from an EF Core query
- it uses `SetProperty()` to define updated columns
- it can update one or multiple properties
- it can use filters to control which rows are updated
- it does not update tracked entity instances already loaded in memory
- it is best for set-based updates that can be translated to SQL

Use `SaveChangesAsync()` when you need the normal tracked entity workflow.

Use [BulkUpdate](https://entityframework-extensions.net/bulk-update) from Entity Framework Extensions when each row has different values coming from entities in memory or when you need advanced bulk options.

## Related Articles

If you want to compare `ExecuteUpdateAsync()` with the most closely related EF Core saving patterns, these pages are the best next step:

- [SaveChanges](/saving/save-changes) — how EF Core persists tracked changes
- [Updating Data](/saving/modifying-data) — how to update entities with the normal tracked workflow
- [ChangeTracker](/saving/change-tracker) — how EF Core stores tracking information internally
- [Tracking Changes of Entities](/saving/change-tracker-how-it-works) — how EF Core detects changes before saving
- [ExecuteDelete](/saving/execute-delete) — how to delete rows directly in the database without loading entities
- [EF Core 10: What’s New](/efcore/efcore-10-what-is-new#executeupdateasync-update-to-allow-non-expression-lambdas) — ExecuteUpdateAsync improvements in EF Core 10

## FAQ

### Does ExecuteUpdateAsync require SaveChangesAsync?

No. `ExecuteUpdateAsync()` executes immediately in the database. You do not need to call `SaveChangesAsync()` afterward.

### Does ExecuteUpdateAsync use the ChangeTracker?

No. `ExecuteUpdateAsync()` does not use the `ChangeTracker` and does not update tracked entity instances already loaded in memory.

### Can ExecuteUpdateAsync update multiple columns?

Yes. You can chain multiple `SetProperty()` calls to update several columns in the same database command.

### Can ExecuteUpdateAsync use the current column value?

Yes. The new value can be based on the current database value, as long as the expression can be translated to SQL.

### When should I use ExecuteUpdateAsync instead of SaveChangesAsync?

Use `ExecuteUpdateAsync()` when you want to update rows directly in the database using a query. Use `SaveChangesAsync()` when you are working with tracked entities and need the normal EF Core change-tracking workflow.

### Is ExecuteUpdateAsync the same as BulkUpdate?

No. `ExecuteUpdateAsync()` is a set-based update API. A [bulk update](https://entityframework-extensions.net/bulk-update) library is usually better when each row has different values coming from a list of entities in memory.