---
title: Modifying data via the ExecuteUpdate
description: Updates all database rows for the entity instances which match the LINQ query from the database. 
canonical: /dbset/execute-update
status: Published
lastmod: 2025-08-22
---

# EF Core Execute Update

By default, EF Core tracks changes to entities, and then sends updates to the database when one of the `SaveChanges` methods is called. 

 - Changes are only sent for properties and relationships that have changed. 
 - The tracked entities remain in sync with the changes sent to the database. 
 - It is an efficient and convenient way to send general-purpose inserts, updates, and deletes to the database. 
 - These changes are also batched to reduce the number of database round-trips.

However, it is sometimes useful to execute update or delete commands on the database without involving the change tracker. 

## DbSet.ExecuteUpdate

EF Core 7.0 provides a new method called the `ExecuteUpdate` method that updates entities in the database based on the results of that query. 

 - The specific changes to make must be specified explicitly; they are not automatically detected by EF Core.
 - Any tracked entities will not be kept in sync.
 - Additional commands may need to be sent in the correct order so as not to violate database constraints. For example, update dependents before a principal can be deleted.
 - All of this means that the `ExecuteUpdate` method complements, rather than replaces, the existing `SaveChanges` mechanism.

The `ExecuteUpdate` behaves in a very similar way to the [ExecuteDelete](/dbset/execute-delete) method. The main difference is that an update requires knowing which properties to update, and how to update them. This is achieved using one or more calls to `SetProperty`. 

The following example updates the `Name` property of every author.

```csharp
using (var context = new LibraryContext())
{
    context.Authors.ExecuteUpdate(
        s => s.SetProperty(b => b.Name, b => b.Name + " *Updated!*"));
}
```

The first parameter of `SetProperty` specifies which property to update; in this case, `Author.Name`. The second parameter specifies how the new value should be calculated; in this case, by taking the existing value and appending "*Updated!*". The resulting SQL is:

```csharp
UPDATE [a]
    SET [a].[Name] = [a].[Name] + N' *Updated!*'
FROM [Authors] AS [a]
```

It also allows you to use the query that can be used to filter which entities are updated. You can call the `SetProperty` method multiple times to update more than one property on the target entity. 

The following example updates the `Title` and `Content` of all the books published before 2018.

```csharp
using (var context = new LibraryContext())
{
    context.Books
        .Where(b => b.PublishedOn.Year < 2018)
        .ExecuteUpdateAsync(s => s
            .SetProperty(a => a.Title, a => a.Title + " (" + a.PublishedOn.Year + ")")
            .SetProperty(a => a.Content, a => a.Content + " ( This content was published in " + b.PublishedOn.Year + ")"));
}
```

In this case, the generated SQL is a bit more complicated:

```csharp
UPDATE [b]
    SET [b].[Content] = (([b].[Content] + N' ( This content was published in ') + COALESCE(CAST(DATEPART(year, [b].[PublishedOn]) AS nvarchar(max)), N'')) + N')',
    [b].[Title] = (([b].[Title] + N' (') + COALESCE(CAST(DATEPART(year, [b].[PublishedOn]) AS nvarchar(max)), N'')) + N')'
FROM [Books] AS [b]
WHERE DATEPART(year, [b].[PublishedOn]) < 2018
```

The `ExecuteDelete` also allows you to use the filter that can reference other tables. The following example updates all tags from old posts.

```csharp
using (var context = new BloggingContext())
{
    context.Tags
        .Where(t => t.Posts.All(e => e.PublishedOn.Year < 2022))
        .ExecuteUpdateAsync(s => s.SetProperty(t => t.Text, t => t.Text + " (old)"));
}
```

When the above code is executed, it will generate the following SQL statement. 

```csharp
UPDATE [t]
    SET [t].[Text] = [t].[Text] + N' (old)'
FROM [Tags] AS [t]
WHERE NOT EXISTS (
    SELECT 1
    FROM [PostTag] AS [p]
    INNER JOIN [Posts] AS [p0] ON [p].[PostsId] = [p0].[Id]
    WHERE [t].[Id] = [p].[TagsId] AND NOT (DATEPART(year, [p0].[PublishedOn]) < 2022))
```

Here’s a drop-in section you can paste into your article.

---

## Is ExecuteUpdate a real “Bulk Update”?

**Short answer: not quite.** `ExecuteUpdate` is a great *set-based* update API built into EF Core: it sends **one SQL statement** that applies **the same update rule** to all rows matching your LINQ filter.
A [real bulk update](/bulk-extensions/bulk-update) (e.g., with [Entity Framework Extensions](https://entityframework-extensions.net/bulk-update)) is a different class of operation designed for **very large datasets** and **per-row values**, using high-throughput database paths (bulk copy/TVPs, temp tables, `MERGE`, batched `UPDATE JOIN`s) plus a rich feature layer.

### What’s the practical difference?

| Aspect                   | `ExecuteUpdate` (EF Core)                                                                                              | Real Bulk Update ([Entity Framework Extensions](https://entityframework-extensions.net/bulk-update))                                                         |
| ------------------------ | ---------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------ |
| How values are supplied  | **Single rule** expressed in LINQ (e.g., `SetProperty(p => p.Price, p => p.Price * 0.9)` applies to all targeted rows) | **Per-entity values** pushed from your in-memory list (each row can get a different value)             |
| Data path                | Single SQL `UPDATE … WHERE …`                                                                                          | Stages your data (TVP/temp table/bulk copy), then **`UPDATE JOIN`/`MERGE`** for maximal throughput     |
| Scale & performance      | Fast for set updates; good up to hundreds of thousands of rows                                                         | Optimized for **millions** of rows; minimizes round-trips and log pressure                             |
| Related data             | Not supported                                                                                                          | **IncludeGraph** to update related entities correctly                                                  |
| Mapping control          | Limited (set columns you specify)                                                                                      | Fine-grained column include/exclude, key selection, identity handling, computed columns behavior, etc. |
| Orchestration & batching | No batch knobs                                                                                                         | **BatchSize**, streaming, command timeouts, retry policy hooks                                         |
| Events & auditing        | None                                                                                                                   | **Pre*/Post* events\*\*, audit stamps, custom logging hooks                                            |
| Entity tracking          | Does not sync tracked instances                                                                                        | Options to **sync back** generated values or reload as needed                                          |

### A quick mental model

* **`ExecuteUpdate`** = “Tell the database: *update all matching rows like **this***.”
  Great for **uniform** changes (e.g., “mark all expired coupons as inactive”).
* **Real bulk update** = “Take **this list** of entities with **their own values**, ship them efficiently to the server, and **apply each value to its matching row**.”
  Essential when **each row is different** or volumes are huge.

### Concrete example

**Goal:** update 1,000,000 products with **different** `NewPrice` values coming from memory.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

// Per-row values (each product has its own NewPrice)
var products = GetMillionProductsWithNewPrices();

// Real bulk update: pushes values, uses staging + UPDATE JOIN under the hood
context.BulkUpdate(products, options =>
{
    options.ColumnInputExpression = p => new { p.Id, p.NewPrice }; // update only needed columns
    options.BatchSize = 10000;
});
```

Doing the same with `ExecuteUpdate` **is not feasible** without first staging your values yourself (e.g., creating a temp table, loading it, then hand-writing an `UPDATE JOIN`). EF Extensions automates that pipeline for you.

### When to use which?

* Use **`ExecuteUpdate`** when:

  * The change is **uniform** for all matched rows.
  * You want a **zero-dependency** solution inside EF Core.
  * The dataset is modest to medium and you don’t need advanced options.

* Use a **real bulk update** (Entity Framework Extensions) when:

  * Each row needs a **different value** from your in-memory data.
  * You’re touching **hundreds of thousands to millions** of rows.
  * You need **upsert/merge**, **graph updates**, or rich **auditing/logging** and **batch controls**.

> Bottom line: `ExecuteUpdate` is a powerful **set-based** tool. A **real bulk update** is a **high-throughput data pipeline** built for scale, per-row values, and advanced orchestration. They complement each other rather than compete.

