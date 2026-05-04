---
title: EF Core ExecuteDelete (EF Core 7–10) – Set-Based Bulk Deletes
description: Discover how to use the ExecuteDelete method starting from EF Core 7 to delete database rows efficiently—no tracking, no SaveChanges needed.
canonical: /saving/execute-delete
status: Published
lastmod: 2026-05-04
---

# EF Core ExecuteDelete

`ExecuteDeleteAsync()` deletes rows directly in the database without loading entities into memory, without tracking them, and without calling [`SaveChangesAsync()`](/saving/save-changes).

It is useful when you want to delete all rows that match a query.


## Delete Rows with a Filter

Most of the time, you will combine `Where()` with `ExecuteDeleteAsync()`.

```csharp
using var context = new AppDbContext();

await context.Blogs
    .Where(b => b.Rating < 3)
    .ExecuteDeleteAsync();
```


Executed SQL:

```sql
DELETE FROM [a]
FROM [Blogs] AS [a]
WHERE [a].[Rating] < 3
```

Only rows that match the filter are deleted.



This is the safest and most common `ExecuteDeleteAsync()` pattern because the delete operation is limited by a query condition.

## Delete All Rows

If you call `ExecuteDeleteAsync()` directly from a `DbSet`, EF Core deletes every row in that table.

```csharp
using var context = new AppDbContext();

await context.Blogs
    .ExecuteDeleteAsync();
```

This executes a database `DELETE` for all `Blogs`.

```sql
DELETE FROM [a]
FROM [Blogs] AS [a]
```

Use this very carefully because no filter is applied.

## Delete with a Navigation Filter

You can use navigation data in the query filter before calling `ExecuteDeleteAsync()`.

```csharp
using var context = new AppDbContext();

await context.Blogs
	.Where(b => b.Posts.All(p => p.PublishedOn.Year < 2018))
	.ExecuteDeleteAsync();
```

This deletes all blogs which contains only posts published before 2018.

The filter is translated to SQL and executed directly in the database.

## Rows Affected

`ExecuteDeleteAsync()` returns an `int` value that indicates the number of rows deleted.

```csharp
using var context = new AppDbContext();

var rowsAffected = await context.Blogs
    .Where(b => b.Rating < 3)
    .ExecuteDeleteAsync();
```

You can use this value for logging, validation, or checking whether the delete matched the expected rows.

## Important Behavior

`ExecuteDeleteAsync()` executes immediately.

That means:

- it does not require for `SaveChangesAsync()`
- it does not use the `ChangeTracker`
- it does not load entities into memory
- it does not remove tracked entity instances already loaded in memory
- it sends a `DELETE` command directly to the database

For tracked entity workflows, see [SaveChanges](/saving/save-changes).

For normal tracked deletes, see [Deleting Data](/saving/deleting-data).

For a deeper explanation of tracking behavior, see [ChangeTracker](/saving/change-tracker).

## ExecuteDelete Requirements

`ExecuteDeleteAsync()` is available starting with EF Core 7.

_(For older versions of EF Core (EF Core 2+), or for unsupported providers, you can use **[DeleteFromQuery (Entity Framework Extensions)](https://entityframework-extensions.net/delete-from-query)**.)_

To use it correctly, keep these requirements in mind:

- the delete must be based on an EF Core query
- the query must be translatable to SQL
- the operation runs immediately in the database
- tracked entities already loaded in the current `DbContext` are not automatically removed from memory
- `SaveChangesAsync()` is not required after calling `ExecuteDeleteAsync()`
- database constraints, foreign keys, and cascade delete rules still apply

For example, this works because the filter can be translated to SQL:

```csharp
using var context = new AppDbContext();

await context.Blogs
    .Where(b => b.Rating < 3)
    .ExecuteDeleteAsync();
```

This deletes matching rows directly in the database.

However, custom .NET methods that cannot be translated to SQL cannot be used in the query filter.

```csharp
using var context = new AppDbContext();

await context.Blogs
    .Where(b => IsLowQuality(b))
    .ExecuteDeleteAsync();
```

This kind of expression cannot be translated unless the method is mapped or translated by the provider.

## How ExecuteDelete Works

`ExecuteDeleteAsync()` sends a `DELETE` command directly to the database.

It does not follow the normal tracked entity workflow.

A normal tracked delete usually looks like this:

```csharp
using var context = new AppDbContext();

var blog = await context.Blogs
    .FirstAsync(b => b.Name == "Old Blog");

context.Blogs.Remove(blog);

await context.SaveChangesAsync();
```

In that workflow, EF Core:

1. loads the entity
2. tracks it in the `DbContext`
3. marks it as `Deleted`
4. generates the delete when `SaveChangesAsync()` runs

`ExecuteDeleteAsync()` skips that workflow.

```csharp
using var context = new AppDbContext();

await context.Blogs
    .Where(b => b.Name == "Old Blog")
    .ExecuteDeleteAsync();
```

In this case, EF Core translates the query into a direct database command.

No entity instance is loaded, and the `ChangeTracker` is not involved.

### ExecuteDelete vs SaveChanges

`ExecuteDeleteAsync()` and `SaveChangesAsync()` can both delete data, but they are designed for different workflows.

| Feature                                     | `ExecuteDeleteAsync()` | `SaveChangesAsync()`     |
| ------------------------------------------- | ---------------------- | ------------------------ |
| Loads entities                              | No                     | Usually yes              |
| Uses ChangeTracker                          | No                     | Yes                      |
| Requires `SaveChangesAsync()`               | No                     | It is the save operation |
| Best for                                    | Set-based deletes      | Tracked entity workflows |
| Removes in-memory tracked entities          | No                     | Yes                      |
| Can use per-entity business logic in memory | No                     | Yes                      |

Use `ExecuteDeleteAsync()` when you want to delete rows directly in the database.

Use `SaveChangesAsync()` when you are working with tracked entities and want EF Core to manage the normal delete workflow during a unit of work.

For the tracked entity workflow, see [SaveChanges](/saving/save-changes).

For normal tracked deletes, see [Deleting Data](/saving/deleting-data).

## ExecuteDelete vs BulkDelete

Is `ExecuteDelete` a real “Bulk Delete”? Not quite. Yes, it deletes rows in bulk directly in SQL. However, it **does not** delete entities from a list with per-row control like [BulkDelete](https://entityframework-extensions.net/bulk-delete) (Entity Framework Extensions).

`ExecuteDeleteAsync()` is a set-based delete API built into EF Core.

It is useful when every matching row can be deleted using the same SQL-translatable query.

```csharp
using var context = new AppDbContext();

await context.Blogs
    .Where(b => b.Rating < 3)
    .ExecuteDeleteAsync();
```

This works well because the delete is expressed as one database rule.

However, `ExecuteDeleteAsync()` is not the same as a full bulk delete library.

Use [BulkDelete](https://entityframework-extensions.net/bulk-delete) from Entity Framework Extensions when:

* the rows to delete come from a list of entities in memory
* you need to delete many specific entities by key
* you need advanced options such as batching, auditing, custom mappings, or output values
* you need more control over large data synchronization scenarios

In short:

* use `ExecuteDeleteAsync()` for set-based deletes expressed as a query
* use `BulkDelete` when you need to delete many specific entities from memory

| Question                       | `ExecuteDeleteAsync()`         | [BulkDelete](https://entityframework-extensions.net/bulk-delete) (**EF Extensions**) |
| ------------------------------ | ------------------------------ | ---------------------------------------------------------------- |
| How are rows selected?         | Query filter translated to SQL | Entity list or key list from memory                              |
| Where does the data come from? | Database query                 | In-memory entity list                                            |
| Different rows by key list?    | Not directly                   | Yes                                                              |
| Uses ChangeTracker?            | No                             | Not the normal EF Core tracking workflow                         |
| Best for                       | Set-based deletes              | High-volume deletes with entity/key lists                        |

### Performance Benchmarks

Deleting **100,000 rows**:

| Method                       |    Time | Memory   |
| ---------------------------- | ------: | -------- |
| `ExecuteDelete`              |  200 ms | Very low |
| `BulkDelete` (EF Extensions) | 1050 ms | Low      |
| `SaveChanges`                | 2250 ms | High     |

[Benchmark Source](https://github.com/zzzprojects/learnentityframeworkcore/blob/main/benchmarks/EFCore/EFCore.Benchmarks/Benchmarks/ExecuteDeleteVsSaveChanges.cs)

> **Important:** For `BulkDelete` and `SaveChanges`, part of the time is spent materializing the list of entities from the database before the delete starts. This step is required because `BulkDelete` and `SaveChanges` work from entities, not from a LINQ rule.

**Takeaway:** `ExecuteDelete` can be much faster than the traditional tracked approach when the delete can be expressed as a set-based SQL rule. `BulkDelete` remains more appropriate when the rows to delete come from an entity list or key list in memory.

### Bulk Delete - Concrete Example

`ExecuteDeleteAsync()` works well when the rows to delete can be selected by a SQL-translatable query.

A real [bulk delete](https://entityframework-extensions.net/bulk-delete) from EF Extensions is different: the entities or keys to delete can come from memory.

For example, imagine you imported a CSV file containing 1,000,000 customer IDs that must be deleted.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

var customers = GetCustomersToDeleteFromCsv();

context.BulkDelete(customers, options =>
{
    options.BatchSize = 10000;
});
```

Doing the same with `ExecuteDeleteAsync()` is not practical unless you build the staging pipeline yourself, such as inserting the IDs into a temporary table and writing a manual `DELETE JOIN`.

[EF Extensions](https://entityframework-extensions.net/) automates that pipeline.

`ExecuteDeleteAsync()` and `BulkDelete` complement each other rather than compete.

## When to Use ExecuteDelete

Use `ExecuteDeleteAsync()` when:

* you want to delete rows directly in the database
* the delete can be expressed as a SQL-translatable query
* you do not need to load entities first
* you do not need the `ChangeTracker`
* you do not need entity instances removed from memory
* you want to delete many rows with one database command

Good examples include:

* deleting old records
* removing inactive rows
* cleaning up temporary data
* deleting records that match a status or date filter
* removing related rows through a query filter

```csharp
using var context = new AppDbContext();

await context.Blogs
    .Where(b => b.LastUpdated < cutoffDate)
    .ExecuteDeleteAsync();
```

## When Not to Use ExecuteDelete

`ExecuteDeleteAsync()` is not the best choice when the delete depends on tracked entity behavior.

Avoid it when:

* you need to delete already loaded entity instances and keep the context state consistent
* you rely on `ChangeTracker` state
* you need entity events or in-memory business logic before deleting
* the rows to delete come from a local entity list or key list
* the query filter cannot be translated to SQL
* you need the normal `SaveChangesAsync()` pipeline

For example, if your application loads an entity, applies domain logic, validates whether it can be deleted, and then removes it, a tracked workflow is usually clearer.

```csharp
using var context = new AppDbContext();

var blog = await context.Blogs
    .FirstAsync(b => b.Name == "Old Blog");

context.Blogs.Remove(blog);

await context.SaveChangesAsync();
```

For normal tracked deletes, see [Deleting Data](/saving/deleting-data).

## Common Pitfalls

Be careful with the following mistakes.

### Expecting SaveChangesAsync to Be Required

`ExecuteDeleteAsync()` runs immediately.

This is unnecessary:

```csharp
using var context = new AppDbContext();

await context.Blogs
    .Where(b => b.Rating < 3)
    .ExecuteDeleteAsync();

// DO NOT USE!
await context.SaveChangesAsync();
```

The `SaveChangesAsync()` call does not save the `ExecuteDeleteAsync()` operation. The delete already happened.

### Deleting All Rows by Mistake

Calling `ExecuteDeleteAsync()` directly from a `DbSet` deletes every row in that table.

```csharp
using var context = new AppDbContext();

await context.Blogs.ExecuteDeleteAsync();
```

Use a `Where()` filter unless you really intend to delete all rows.

### Expecting Tracked Entities to Be Removed from Memory

If an entity is already tracked, `ExecuteDeleteAsync()` does not automatically remove that in-memory instance.

```csharp
using var context = new AppDbContext();

var blog = await context.Blogs
    .FirstAsync(b => b.Name == "Old Blog");

await context.Blogs
    .Where(b => b.Name == "Old Blog")
    .ExecuteDeleteAsync();

// The tracked blog instance may still exist in the current DbContext.
```

If you need a clean view of the database state, use a new `DbContext`, detach the entity, or reload data as needed.

### Ignoring Foreign Keys and Cascade Rules

`ExecuteDeleteAsync()` sends a direct database `DELETE`.

Foreign key constraints and database cascade rules still apply.

If related rows block the delete, the database can reject the command.

If cascade delete is configured, related rows may also be deleted according to the configured database rules.

For more information about related data deletion, see [Deleting Data](/saving/deleting-data).

## ExecuteDelete Release History

* [EF Core 8.0](https://learn.microsoft.com/en-us/ef/core/what-is-new/ef-core-8.0/whatsnew#better-executeupdate-and-executedelete): Improved `ExecuteUpdate` and `ExecuteDelete` to support more complex queries (owned types, unions, and TPT), as long as all operations target a single database table.
* [EF Core 7.0](https://learn.microsoft.com/en-us/ef/core/what-is-new/ef-core-7.0/whatsnew#executeupdate-and-executedelete-bulk-updates): Introduced `ExecuteUpdate` and `ExecuteDelete` as native set-based operations in EF Core.
* **EF Core 2.0+:** For older versions of EF Core, or for unsupported providers, you can use **[DeleteFromQuery (Entity Framework Extensions)](https://entityframework-extensions.net/delete-from-query)**.

`ExecuteDeleteAsync()` was introduced in EF Core 7.

The main idea is the same across EF Core 7, 8, 9, 10 and later versions: delete matching rows directly in the database without loading entities.

However, newer EF Core versions may improve translation support, provider behavior, and supported query shapes.

The exact behavior can still depend on the database provider.

## External Resources - ExecuteDelete

The following videos are useful if you want to see `ExecuteDeleteAsync()` in real EF Core examples, especially how it differs from tracked deletes with `SaveChangesAsync()` and how it avoids unnecessary entity loading.

### Video 1 - Entity Framework 7 - Bulk Editing

<iframe width="560" height="315" src="https://www.youtube.com/embed/A5_thTxsCjY?si=IhIioXL8AplArLl1" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

_by @CodingTutorialsAreGo ([Jasper Kent](/contributors/jasper-kent))_

Jasper Kent introduces the EF Core 7 set-based execute methods and shows how `ExecuteDelete` can replace traditional delete workflows that load entities, remove them, and call `SaveChanges`. He also compares the generated SQL and shows how to retrieve the number of affected rows.

Key timestamps:

- [0:00](https://www.youtube.com/watch?v=A5_thTxsCjY&t=0) — Overview of `ExecuteDelete` and `ExecuteUpdate`
- [0:34](https://www.youtube.com/watch?v=A5_thTxsCjY&t=34) — Project setup
- [2:09](https://www.youtube.com/watch?v=A5_thTxsCjY&t=129) — Traditional way to delete
- [7:19](https://www.youtube.com/watch?v=A5_thTxsCjY&t=439) — Implementing `ExecuteDelete`
- [24:35](https://www.youtube.com/watch?v=A5_thTxsCjY&t=1475) — Conclusion and performance insights

Sample repository:

- [JasperKent/Ef7-Bulk-Actions](https://github.com/JasperKent/Ef7-Bulk-Actions)

### Video 2 - EF Core 7 - Performance Improvements With ExecuteUpdate and ExecuteDelete

<iframe width="560" height="315" src="https://www.youtube.com/embed/VYitXAc_htI?si=SbQjx3-iJPxCjRcY" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

Milan Jovanović shows how `ExecuteDelete` removes multiple records in a single SQL `DELETE` without loading entities. He demonstrates a conditional delete, the generated SQL, and the efficiency compared to the classic deletion loop.

Key timestamps:

- [7:00](https://www.youtube.com/watch?v=VYitXAc_htI&t=420) — Building the delete endpoint using `ExecuteDelete`
- [8:25](https://www.youtube.com/watch?v=VYitXAc_htI&t=505) — Traditional delete pattern and why it is inefficient
- [8:45](https://www.youtube.com/watch?v=VYitXAc_htI&t=525) — Running `ExecuteDelete` with a conditional filter
- [9:30](https://www.youtube.com/watch?v=VYitXAc_htI&t=570) — Observing the generated SQL
- [10:15](https://www.youtube.com/watch?v=VYitXAc_htI&t=615) — SQL `DELETE` produced by EF Core 7
- [10:45](https://www.youtube.com/watch?v=VYitXAc_htI&t=645) — Final explanation of the direct range delete behavior

### Video 3 - Entity Framework 7 Makes Performing Updates and Deletes Easy

<iframe width="560" height="315" src="https://www.youtube.com/embed/yDFwJ7JfG3s?si=MkVDh5sv3lP5Hw-1" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

Israel Quiroz demonstrates how `ExecuteDeleteAsync()` removes entities directly in the database using a filtered query, without retrieving them first. He compares the traditional 3-step delete process with the single-roundtrip method and highlights the returned count of affected rows.

Key timestamps:

- [1:40](https://www.youtube.com/watch?v=yDFwJ7JfG3s&t=100) — Traditional delete workflow: find, remove, `SaveChanges`
- [2:00](https://www.youtube.com/watch?v=yDFwJ7JfG3s&t=120) — Replacing the workflow with `ExecuteDeleteAsync`
- [2:15](https://www.youtube.com/watch?v=yDFwJ7JfG3s&t=135) — Returned value: number of deleted rows
- [2:40](https://www.youtube.com/watch?v=yDFwJ7JfG3s&t=160) — One database round-trip vs. multiple calls
- [4:40](https://www.youtube.com/watch?v=yDFwJ7JfG3s&t=280) — Runs without loading or tracking entities

### Video 4 - Spanish: Borrado y Actualizaciones Masivas - Nuevo de EF Core 7

<iframe width="560" height="315" src="https://www.youtube.com/embed/o9B3pxVpPIY?si=FeEfhtc2FFGeOjv9" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

Felipe Gavilan shows how `ExecuteDelete` removes thousands of rows directly in SQL Server without loading entities. He demonstrates a full-table delete, a filtered delete, and a performance comparison with `RemoveRange`.

Key timestamps:

- [1:47](https://www.youtube.com/watch?v=o9B3pxVpPIY&t=107) — Full-table delete using `ExecuteDelete`
- [3:16](https://www.youtube.com/watch?v=o9B3pxVpPIY&t=196) — Conditional delete using `Where(...).ExecuteDelete()`
- [4:32](https://www.youtube.com/watch?v=o9B3pxVpPIY&t=272) — Verifying filtered results in SQL Server
- [8:31](https://www.youtube.com/watch?v=o9B3pxVpPIY&t=511) — Starting stopwatch for performance comparison
- [9:31](https://www.youtube.com/watch?v=o9B3pxVpPIY&t=571) — Final numbers: 100k deletes

### Video 5 - Do You Know The Fastest Way To Delete Data With EF Core?

<iframe width="560" height="315" src="https://www.youtube.com/embed/hSSatvEOp3w?si=hF9Ha3fTBT2gg9tE" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

Milan Jovanović compares three delete approaches and shows why `ExecuteDeleteAsync()` is often the most efficient option for set-based deletes. He highlights SQL generation, tracking behavior, concurrency issues, and real-world cleanup scenarios.

Key timestamps:

- [0:45](https://www.youtube.com/watch?v=hSSatvEOp3w&t=45) — Traditional delete workflow: find, remove, `SaveChanges`
- [3:00](https://www.youtube.com/watch?v=hSSatvEOp3w&t=180) — Second approach: attach and mark as deleted
- [8:32](https://www.youtube.com/watch?v=hSSatvEOp3w&t=512) — Introducing `ExecuteDeleteAsync`
- [8:45](https://www.youtube.com/watch?v=hSSatvEOp3w&t=525) — Demo: one SQL query, no tracking
- [9:15](https://www.youtube.com/watch?v=hSSatvEOp3w&t=555) — Final conclusion: most performant delete method

## Summary

`ExecuteDeleteAsync()` lets you delete rows directly in the database without loading entities, tracking them, or calling `SaveChangesAsync()`.

Key points:

- it executes immediately
- it works from an EF Core query
- it deletes all rows that match the query
- it does not use the `ChangeTracker`
- it does not remove tracked entity instances already loaded in memory
- database constraints, foreign keys, and cascade rules still apply
- it is best for set-based deletes that can be translated to SQL

Use `SaveChangesAsync()` when you need the normal tracked entity delete workflow.

Use [BulkDelete](https://entityframework-extensions.net/bulk-delete) from Entity Framework Extensions when the rows to delete come from an entity list or key list in memory, or when you need advanced bulk options.

## Related Articles

If you want to compare `ExecuteDeleteAsync()` with the most closely related EF Core saving patterns, these pages are the best next step:

- [ExecuteUpdate](/saving/execute-update) — how to update rows directly in the database without loading entities
- [SaveChanges](/saving/save-changes) — how EF Core persists tracked changes
- [Deleting Data](/saving/deleting-data) — how to delete entities with the normal tracked workflow
- [ChangeTracker](/saving/change-tracker) — how EF Core stores tracking information internally
- [Tracking Changes of Entities](/saving/change-tracker-how-it-works) — how EF Core detects changes before saving

## FAQ

### Does ExecuteDeleteAsync require SaveChangesAsync?

No. `ExecuteDeleteAsync()` executes immediately in the database. You do not need to call `SaveChangesAsync()` afterward.

### Does ExecuteDeleteAsync use the ChangeTracker?

No. `ExecuteDeleteAsync()` does not use the `ChangeTracker` and does not remove tracked entity instances already loaded in memory.

### Can ExecuteDeleteAsync delete rows with a filter?

Yes. You can use `Where()` before `ExecuteDeleteAsync()` to delete only rows that match a condition.

### Can ExecuteDeleteAsync delete all rows?

Yes. Calling `ExecuteDeleteAsync()` directly from a `DbSet` deletes every row in that table. Use this carefully because no filter is applied.

### What happens with foreign keys and cascade delete?

`ExecuteDeleteAsync()` sends a direct `DELETE` command to the database. Foreign key constraints and database cascade rules still apply.

### When should I use ExecuteDeleteAsync instead of SaveChangesAsync?

Use `ExecuteDeleteAsync()` when you want to delete rows directly in the database using a query. Use `SaveChangesAsync()` when you are working with tracked entities and need the normal EF Core change-tracking workflow.

### Is ExecuteDeleteAsync the same as BulkDelete?

No. `ExecuteDeleteAsync()` is a set-based delete API. A [BulkDelete](https://entityframework-extensions.net/bulk-delete) (from EF Extensions) is a better option when the rows to delete come from a list of entities or keys in memory.