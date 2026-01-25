---
title: EF Core ExecuteUpdate (EF Core 7–10) – Set-Based Bulk Updates
description: Learn how EF Core ExecuteUpdate works from EF Core 7 to EF Core 10. Perform fast, set-based SQL updates without tracking, plus new JSON and lambda features.
canonical: /dbset/execute-update
status: Published
lastmod: 2026-01-16
---

# EF Core Execute Update

## What is ExecuteUpdate

`ExecuteUpdate` is an EF Core feature introduced in EF Core 7 (see also [ExecuteDelete](/dbset/execute-delete)) that lets you update database rows **directly in SQL** without loading entities into memory, without using the Change Tracker, and without calling `SaveChanges`.

Instead of modifying entities and calling `SaveChanges`, you define your update logic through LINQ. You use the `SetProperty` method to choose which property to update and what value to set. EF Core then translates everything into a single `UPDATE` statement executed on the database server.

`ExecuteUpdate` is available in both synchronous and asynchronous versions: `ExecuteUpdate` and `ExecuteUpdateAsync`.

**Basic example:**

```csharp
await context.Customers
    .Where(c => c.Age > 30)
    .ExecuteUpdateAsync(s =>
        s.SetProperty(c => c.Age, c => c.Age + 1));
```

Generated SQL:

```sql
UPDATE [c]
    SET [c].[Age] = [c].[Age] + 1
FROM [Customers] AS [c]
WHERE [c].[Age] > 30
```

In short, it tells the database: **"update all matching rows like this."**

This makes updates dramatically faster and is ideal for bulk or set-based operations.

## ExecuteUpdate Requirements

* **EF Core Version:** EF Core **7.0+**
* **Supported Providers:** SQL Server, SQLite, PostgreSQL, MySQL, Oracle
* **Unsupported Providers:** MariaDB, InMemory
* **Additional Requirements:**
  * Update logic must be **SQL-translatable**
  * Expressions in `SetProperty` must not rely on custom C# functions
  * You must specify properties explicitly (EF does not detect changes)

### SQL-translatable expressions (important)

For `SetProperty`, expressions must be translatable to SQL.

**Supported:**

* Constants
* Column references
* Basic math and string operations

**Not supported:**

* Arbitrary C# methods
* Business logic
* Random values
* Values coming from in-memory collections

## TL;DR – ExecuteUpdate

* Runs **set-based updates** directly in SQL.
* Executes **immediately** (does **not** require calling `SaveChanges`).
* Does **not** load entities into memory (massive performance boost).
* Does **not** use the Change Tracker.
* Does **not** update tracked entities already in memory.
* Update rules must be **SQL-translatable**.
* Best for batch updates and simple value transformations.
* EF Extensions supports [real bulk updates](https://entityframework-extensions.net/bulk-update) when **per-row values** are needed.

## A quick mental model

* **`ExecuteUpdate`** = “Tell the database: *update all matching rows like **this***.” Great for **uniform update rules** (the same transformation applied to every matched row).

* **[`BulkUpdate` (EF Extensions)](https://entityframework-extensions.net/bulk-update)** = “Take **this list** of entities with **their own values**, ship them efficiently to the server, and **apply each value to its matching row**.” Essential when **each row is different** or volumes are huge.

## When to use vs when NOT to use

| Use `ExecuteUpdate` when…                                 | Avoid `ExecuteUpdate` when…                                             |
| --------------------------------------------------------- | ----------------------------------------------------------------------- |
| The update rule is **uniform** for all matching rows      | Each row needs a **different value** coming from memory                 |
| You want **one SQL UPDATE** and minimal round-trips       | You need a **high-throughput pipeline** (staging + UPDATE JOIN / MERGE) |
| You want a **zero-dependency** EF Core solution           | You need **graph updates** (related entities)                           |
| You’re doing maintenance tasks (cleanup, flags, counters) | You need **auditing, batching knobs, orchestration** features           |
| You are OK reloading tracked entities if needed           | You require tracked instances to stay automatically in sync             |

For scenarios that `ExecuteUpdate` does not support—such as per-row values coming from memory, very large datasets, or advanced orchestration—you should use [`BulkUpdate` (EF Extensions)](https://entityframework-extensions.net/bulk-update) for a fast and efficient bulk update pipeline.

> **Important:** `ExecuteUpdate` is **not a replacement** for `BulkUpdate`. They solve different classes of problems and are complementary.

## ExecuteUpdate Examples

### Update with concatenation

This example appends `" *Updated!*"` to the `Name` of every author:

```csharp
using (var context = new LibraryContext())
{
    await context.Authors.ExecuteUpdateAsync(s =>
        s.SetProperty(a => a.Name, a => a.Name + " *Updated!*"));
}
```

Generated SQL:

```sql
UPDATE [a]
    SET [a].[Name] = [a].[Name] + N' *Updated!*'
FROM [Authors] AS [a]
```

### Update multiple columns with a filter

You can chain `SetProperty` calls to update more than one property.

This example updates `Title` and `Content` for all books published before 2018:

```csharp
using (var context = new LibraryContext())
{
    await context.Books
        .Where(b => b.PublishedOn.Year < 2018)
        .ExecuteUpdateAsync(s => s
            .SetProperty(b => b.Title, b => b.Title + " (" + b.PublishedOn.Year + ")")
            .SetProperty(b => b.Content, b => b.Content + " (This content was published in " + b.PublishedOn.Year + ")"));
}
```

Generated SQL:

```sql
UPDATE [b]
    SET [b].[Content] = (([b].[Content] + N' (This content was published in ') + COALESCE(CAST(DATEPART(year, [b].[PublishedOn]) AS nvarchar(max)), N'')) + N')',
        [b].[Title] = (([b].[Title] + N' (') + COALESCE(CAST(DATEPART(year, [b].[PublishedOn]) AS nvarchar(max)), N'')) + N')'
FROM [Books] AS [b]
WHERE DATEPART(year, [b].[PublishedOn]) < 2018
```

### Update using navigation filters

`ExecuteUpdate` also allows filters that reference other tables.

This example updates tags only for posts published before 2022:

```csharp
using (var context = new BloggingContext())
{
    await context.Tags
        .Where(t => t.Posts.All(p => p.PublishedOn.Year < 2022))
        .ExecuteUpdateAsync(s =>
            s.SetProperty(t => t.Text, t => t.Text + " (old)"));
}
```

Generated SQL:

```sql
UPDATE [t]
    SET [t].[Text] = [t].[Text] + N' (old)'
FROM [Tags] AS [t]
WHERE NOT EXISTS (
    SELECT 1
    FROM [PostTag] AS [p]
    INNER JOIN [Posts] AS [p0] ON [p].[PostsId] = [p0].[Id]
    WHERE [t].[Id] = [p].[TagsId] AND NOT (DATEPART(year, [p0].[PublishedOn]) < 2022))
```

## ExecuteUpdate – EF Core 10 Enhancement

Starting in EF Core 10, `ExecuteUpdateAsync` accepts a **regular lambda**, not only expression trees.

This makes dynamic/conditional updates easier to write, as long as every `SetProperty` remains **SQL-translatable**.

```csharp
bool nameChanged = true; // Local variable (not a column)

await context.Blogs.ExecuteUpdateAsync(s =>
{
    s.SetProperty(b => b.Views, 8);

    if (nameChanged)
    {
        s.SetProperty(b => b.Name, "foo");
    }
});
```

## ExecuteUpdate Release History

* **EF Core 10.0:**
  * [JSON support](https://learn.microsoft.com/en-us/ef/core/what-is-new/ef-core-10.0/whatsnew#executeupdate-support-for-relational-json-columns): Added `ExecuteUpdate` support for relational JSON columns, allowing efficient bulk updates of JSON properties when mapped as complex types.
  * [Easier dynamic updates](https://learn.microsoft.com/en-us/ef/core/what-is-new/ef-core-10.0/whatsnew#executeupdateasync-now-accepts-a-regular-non-expression-lambda): `ExecuteUpdateAsync` now accepts a regular lambda (not only expression trees), making dynamic and conditional updates much easier to write.
* [EF Core 9.0](https://learn.microsoft.com/en-us/ef/core/what-is-new/ef-core-9.0/whatsnew#executeupdate): Improved `ExecuteUpdate` to support complex type properties.
* [EF Core 8.0](https://learn.microsoft.com/en-us/ef/core/what-is-new/ef-core-8.0/whatsnew#better-executeupdate-and-executedelete): Improved `ExecuteUpdate` and `ExecuteDelete` to support more complex queries (owned types, unions, and TPT), as long as all updates target a single database table.
* [EF Core 7.0](https://learn.microsoft.com/en-us/ef/core/what-is-new/ef-core-7.0/whatsnew#executeupdate-and-executedelete-bulk-updates): Introduced `ExecuteUpdate` and `ExecuteDelete`.
* **EF Core 2.0+:** For older versions of EF Core or if you prefer the syntax provided by EF Extensions over using `SetProperty`, you can use [UpdateFromQuery](https://entityframework-extensions.net/update-from-query).

## Is ExecuteUpdate a real “Bulk Update”?

**Short answer: not quite.**

Yes, it updates rows in bulk in the database. However, it **does not** bulk update entities with their own per-row values like [BulkUpdate](https://entityframework-extensions.net/bulk-update) (Entity Framework Extensions).

The difference is simple:

* **`ExecuteUpdate`**: Tell the database, *“update all matching rows like **this**.”*
* **[BulkUpdate](https://entityframework-extensions.net/bulk-update)**: Take this list of entities, each with its own values, send them efficiently to the server, and apply each value to its matching row.

```csharp
// ExecuteUpdate
await context.Customers
    .Where(c => c.Age > 30)
    .ExecuteUpdateAsync(s =>
        s.SetProperty(c => c.Age, c => c.Age + 1));

// BulkUpdate (with EF Extensions)
await context.BulkUpdateAsync(customers);
```

### What’s the practical difference?

| Question                       | `ExecuteUpdate` (EF Core)                   | [BulkUpdate](https://entityframework-extensions.net/bulk-update) (EF Extensions) |
| ------------------------------ | ------------------------------------------- | -------------------------------------------------------------------------------- |
| How are values applied?        | One rule for all rows                       | Values come from each entity                                                     |
| Where does the data come from? | Computed in SQL                             | In-memory entity list                                                            |
| How many rows?                 | Good for thousands to hundreds of thousands | Designed for millions                                                            |
| Different values per row?      | ❌ No                                        | ✅ Yes                                                                            |
| Related entities?              | ❌ No                                        | ✅ Yes (`IncludeGraph`)                                                           |
| Change Tracker sync?           | ❌ No                                        | ✅ Yes (Sync output values + with option)                                              |

### Performance Benchmarks

Updating **100,000 rows** (3 `int` columns + 3 `string` columns):

| Method                       |    Time | Memory   |
| ---------------------------- | ------: | -------- |
| `ExecuteUpdate`              |  365 ms | Very low |
| `BulkUpdate` (EF Extensions) | 1900 ms | Low      |
| `SaveChanges`                | 4800 ms | High     |

[Benchmark Source](https://github.com/zzzprojects/learnentityframeworkcore/blob/main/benchmarks/EFCore/EFCore.Benchmarks/Benchmarks/ExecuteUpdateVsSaveChanges.cs)

> **Important:**
> For `BulkUpdate` and `SaveChanges`, around **250 ms** of this time is spent **materializing the list of entities from the database** before the update starts.
> This step is required because `BulkUpdate` and `SaveChanges` work from entities, not from a LINQ rule.

**Takeaway:** `ExecuteUpdate` can be **an order of magnitude faster** than the traditional tracked approach, while memory usage stays near-constant because entities are never loaded.

### Bulk Update – Concrete Example

**Goal:** update 1,000,000 products with **different** `NewPrice` values coming from memory.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

// Per-row values (each product has its own NewPrice)
var products = GetMillionProductsWithNewPrices();

// Real bulk update: pushes values, uses staging + UPDATE JOIN / MERGE under the hood
context.BulkUpdate(products, options =>
{
    options.ColumnInputExpression = p => new { p.Id, p.NewPrice }; // update only needed columns
    options.BatchSize = 10000;
});
```

Doing the same with `ExecuteUpdate` is **not feasible** without first staging your values yourself (for example, creating a temp table, loading it, then writing an `UPDATE JOIN` manually).

[EF Extensions](https://entityframework-extensions.net/) automates that entire pipeline for you.

### When to use which?

| Scenario                                           | Recommended                                                                                       |
| -------------------------------------------------- | ------------------------------------------------------------------------------------------------- |
| Same update rule for all rows                      | `ExecuteUpdate`                                                                                   |
| Complex update rules                               | `ExecuteUpdate` (if SQL-translatable)                                                             |
| Per-row different values                           | EF Extensions [BulkUpdate](https://entityframework-extensions.net/bulk-update)                    |
| Large-scale data updates                           | `ExecuteUpdate` or EF Extensions [BulkUpdate](https://entityframework-extensions.net/bulk-update) |
| Need events, validation, or Change Tracker updates | `SaveChanges`                                                                                     |


* Use **`ExecuteUpdate`** when:

  * The change is **uniform** for all matched rows
  * You want a **zero-dependency** solution inside EF Core
  * The dataset is modest to medium and you don’t need advanced options

* Use a **[real bulk update](https://entityframework-extensions.net/bulk-update)** (Entity Framework Extensions) when:

  * Each row needs a **different value** from your in-memory data
  * You’re touching **hundreds of thousands to millions** of rows
  * You need **upsert/merge**, **graph updates**, or advanced options

> **Bottom line:** `ExecuteUpdate` is a powerful **set-based** tool.
> A **real bulk update** is a **high-throughput data pipeline** built for scale, per-row values, and advanced orchestration.
> They complement each other rather than compete.

## Additional Resources – ExecuteUpdate

### 📘 Recommended Reading

* [Microsoft - ExecuteUpdate](https://learn.microsoft.com/en-us/ef/core/saving/execute-insert-update-delete#executeupdate)
* [Entity Framework Tutorials - ExecuteUpdate](https://www.entityframeworktutorial.net/efcore/execute-update.aspx)
* [ExecuteUpdateAsync Update to Allow Non-Expression Lambda](https://www.learnentityframeworkcore.com/efcore/efcore-10-what-is-new#executeupdateasync-update-to-allow-non-expression-lambdas)

### 🎥 Recommended Videos

#### EF Core 7 – Performance Improvements With the New ExecuteUpdate & ExecuteDelete, por Milan Jovanović

<iframe width="560" height="315" src="https://www.youtube.com/embed/VYitXAc_htI?si=zfdIx_3RPKQN7WXu" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

Milan demonstrates how to replace a slow foreach-based update with the new ExecuteUpdate API in EF Core 7. He applies a computed value (Salary * 1.1), explains how the update runs directly in the database without tracking entities, and compares performance against the traditional approach.

**Key timestamps:**

* [01:45](https://www.youtube.com/watch?v=VYitXAc_htI&t=105s) – Creating the new endpoint based on ExecuteUpdate
* [02:30](https://www.youtube.com/watch?v=VYitXAc_htI&t=150s) – Filtering employees by CompanyId using LINQ
* [03:00](https://www.youtube.com/watch?v=VYitXAc_htI&t=180s) – Applying a percentage salary increase with SetProperty
* [03:45](https://www.youtube.com/watch?v=VYitXAc_htI&t=225s) – No tracking + no SaveChanges call
* [04:45](https://www.youtube.com/watch?v=VYitXAc_htI&t=285s) – SQL UPDATE generated by EF Core 7
* [06:00](https://www.youtube.com/watch?v=VYitXAc_htI&t=360s) – Performance comparison: foreach update vs ExecuteUpdate

#### Entity Framework 7 Makes Performing UPDATES and DELETES Easy!, por Israel Quiroz

<iframe width="560" height="315" src="https://www.youtube.com/embed/yDFwJ7JfG3s?si=-xRNkhwd9IhRX6pm" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

Israel explains how ExecuteUpdateAsync replaces the traditional workflow of retrieving an entity, modifying properties, and calling SaveChanges. He shows how a single database call can update several fields using SetProperty, without tracking entities.

**Key timestamps:**

* [03:28](https://www.youtube.com/watch?v=yDFwJ7JfG3s&t=208s) – Traditional update workflow (retrieve → assign → SaveChanges)
* [03:40](https://www.youtube.com/watch?v=yDFwJ7JfG3s&t=220s) – ExecuteUpdateAsync with SetProperty
* [04:00](https://www.youtube.com/watch?v=yDFwJ7JfG3s&t=240s) – Chained SetProperty calls (multiple fields)
* [04:20](https://www.youtube.com/watch?v=yDFwJ7JfG3s&t=260s) – Fewer database round-trips
* [04:40](https://www.youtube.com/watch?v=yDFwJ7JfG3s&t=280s) – No change tracking during the update

#### (SPANISH) Borrado y Actualizaciones Masivas – Nuevo de EF Core 7, por Felipe Gavilan

<iframe width="560" height="315" src="https://www.youtube.com/embed/o9B3pxVpPIY?si=z2N0sPCUOE9xBEqp" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

Felipe demonstrates mass updates using ExecuteUpdate with multiple `SetProperty` calls. He updates a timestamp and a string field, verifies the results in SQL Server, and shows how all changes are performed in one consolidated SQL UPDATE.

**Key timestamps:**

* [05:32](https://www.youtube.com/watch?v=o9B3pxVpPIY&t=332s) – First SetProperty: assigning the update timestamp
* [06:48](https://www.youtube.com/watch?v=o9B3pxVpPIY&t=408s) – Second SetProperty: appending text to Nombre
* [07:48](https://www.youtube.com/watch?v=o9B3pxVpPIY&t=468s) – Running the mass update and reviewing results
* [08:31](https://www.youtube.com/watch?v=o9B3pxVpPIY&t=511s) – Beginning of performance comparison
* [09:31](https://www.youtube.com/watch?v=o9B3pxVpPIY&t=571s) – Final numbers: 100k updates (8s → 0.2s)

## Summary & Next Steps – ExecuteUpdate

`ExecuteUpdate` is a powerful EF Core feature that enables fast, set-based updates without Change Tracker overhead. It is ideal for bulk data maintenance and simple transformations.

**Use `ExecuteUpdate` when:**

* You want one uniform update rule applied to many records
* You want performance without loading entities into memory
* You’re OK reloading tracked entities if needed

**Use EF Extensions `BulkUpdate` when:**

* Each row requires a unique value from memory
* You need advanced batching, `MERGE`, graph updates, or auditing
* You are updating very large datasets at high throughput

**Next steps:**

* Explore the [EF Extensions BulkUpdate](https://entityframework-extensions.net/bulk-update) documentation
* Learn about [ExecuteDelete](/dbset/execute-delete)
