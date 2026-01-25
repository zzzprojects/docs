---
title: EF Core ExecuteDelete (EF Core 7–10) – Set-Based Bulk Deletes
description: Discover how to use the ExecuteDelete method starting from EF Core 7 to delete database rows efficiently—no tracking, no SaveChanges needed.
canonical: /dbset/execute-delete
status: Published
lastmod: 2026-01-24
---

# EF Core Execute Delete

## What is ExecuteDelete?

`ExecuteDelete` is an EF Core feature introduced in **EF Core 7** (see also [ExecuteUpdate](/dbset/execute-update)). It allows you to delete database rows **directly in SQL**, without loading entities into memory, without using the Change Tracker, and without calling `SaveChanges`.

Instead of materializing entities and calling `RemoveRange` followed by `SaveChanges`, you define the delete operation using a LINQ query that EF Core translates into a single `DELETE` statement executed on the database server.

`ExecuteDelete` is available in both synchronous and asynchronous forms: `ExecuteDelete` and `ExecuteDeleteAsync`.

**Basic example:**

```csharp
await context.Authors
    .Where(a => a.Name.Contains("ZZZ Projects"))
    .ExecuteDeleteAsync();
```

Generated SQL:

```sql
DELETE FROM [a]
FROM [Authors] AS [a]
WHERE [a].[Name] LIKE N'%ZZZ Projects%'
```

In short, it tells the database: **“delete all rows matching this condition.”**

This makes deletes dramatically faster and is ideal for bulk or set-based operations.

**Tip:** Always start with a filter and validate the generated SQL, especially in production.

## ExecuteDelete Requirements

* **EF Core Version:** EF Core **7.0+**
* **Supported Providers:** SQL Server, SQLite, PostgreSQL, MySQL, Oracle
* **Unsupported Providers:** MariaDB, InMemory

## TL;DR – ExecuteDelete

* Runs **set-based deletes** directly in SQL.
* Executes **immediately** (does **not** require calling `SaveChanges`).
* Does **not** load entities into memory (massive performance boost).
* Does **not** use or update the Change Tracker.
* Database cascades (ON DELETE CASCADE) still apply if configured
* Requires deleting rows in the **correct order** to avoid foreign key violations.
* EF Extensions supports [real bulk deletes](https://entityframework-extensions.net/bulk-delete) for entity-based or large-scale delete scenarios.

## A quick mental model

* **`ExecuteDelete`** = “Tell the database: *delete all rows matching this condition*.” Ideal for **uniform delete rules** (the same rule applied to every matched row).

* **`BulkDelete` [(EF Extensions)](https://entityframework-extensions.net/bulk-delete)** = “Send a specific list of entity keys to the database and delete **exactly those rows** efficiently.” Essential when deleting **specific entities**, working with **very large datasets**, or requiring advanced control.

## When to use vs when NOT to use

| Use `ExecuteDelete` when…                            | Avoid `ExecuteDelete` when…                                     |
| ---------------------------------------------------- | --------------------------------------------------------------- |
| The delete rule is **uniform** for all matching rows | You need to delete a **specific list of entities** from memory  |
| You want **one SQL DELETE** and minimal round-trips  | You need a **high-throughput pipeline** (staging + DELETE JOIN) |
| You want a **zero-dependency** EF Core solution      | You need **graph deletes** (related entities)                   |
| You’re doing cleanup or maintenance deletes          | You need **auditing, batching knobs, orchestration** features   |
| You are OK managing FK order yourself                | You require entity-based control and cascade handling           |

For scenarios that `ExecuteDelete` does **not** support—such as deleting a specific list of entities coming from memory, very large datasets, or advanced orchestration—you should use **`BulkDelete` [(EF Extensions)](https://entityframework-extensions.net/bulk-delete)** for a fast and efficient bulk delete pipeline.

> **Important:**
> `ExecuteDelete` is **not a replacement** for **BulkDelete**.
> They solve different classes of problems and are **complementary**.


## ExecuteDelete Examples

### Delete all rows (use with caution)

This example shows how to call `ExecuteDelete` on a `DbSet`.
It immediately deletes **all rows** from the table.

```csharp
using (var context = new LibraryContext())
{
    await context.Authors.ExecuteDeleteAsync();
}
```

Generated SQL:

```sql
DELETE FROM [a]
FROM [Authors] AS [a]
```

### Delete rows with a filter

You can delete rows matching a predicate.
This example deletes authors whose name contains `"ZZZ Projects"`.

```csharp
using (var context = new LibraryContext())
{
    await context.Authors
        .Where(a => a.Name.Contains("ZZZ Projects"))
        .ExecuteDeleteAsync();
}
```

Generated SQL:

```sql
DELETE FROM [a]
FROM [Authors] AS [a]
WHERE [a].[Name] LIKE N'%ZZZ Projects%'
```

### Delete rows with a complex filter (navigation filter)

More complex filters are supported, including filters based on related data.

This example deletes tags only for posts published before 2018:

```csharp
using (var context = new BloggingContext())
{
    await context.Tags
        .Where(t => t.Posts.All(p => p.PublishedOn.Year < 2018))
        .ExecuteDeleteAsync();
}
```

Generated SQL:

```sql
DELETE FROM [t]
FROM [Tags] AS [t]
WHERE NOT EXISTS (
    SELECT 1
    FROM [PostTag] AS [p]
    INNER JOIN [Posts] AS [p0] ON [p].[PostsId] = [p0].[Id]
    WHERE [t].[Id] = [p].[TagsId]
      AND NOT (DATEPART(year, [p0].[PublishedOn]) < 2018)
)
```

### Return the number of rows affected

`ExecuteDelete` returns the number of rows deleted.

```csharp
using (var context = new LibraryContext())
{
    var rowsDeleted = await context.Authors
        .Where(a => a.Name.Contains("ZZZ Projects"))
        .ExecuteDeleteAsync();

    Console.WriteLine($"Rows deleted: {rowsDeleted}");
}
```

## ExecuteDelete Release History

* [EF Core 8.0](https://learn.microsoft.com/en-us/ef/core/what-is-new/ef-core-8.0/whatsnew#better-executeupdate-and-executedelete): Improved `ExecuteUpdate` and `ExecuteDelete` to support more complex queries (owned types, unions, and TPT), as long as all operations target a single database table.
* [EF Core 7.0](https://learn.microsoft.com/en-us/ef/core/what-is-new/ef-core-7.0/whatsnew#executeupdate-and-executedelete-bulk-updates): Introduced `ExecuteUpdate` and `ExecuteDelete` as native set-based operations in EF Core.
* **EF Core 2.0+:** For older versions of EF Core, or for unsupported providers, you can use **[DeleteFromQuery (Entity Framework Extensions)](https://entityframework-extensions.net/delete-from-query)**.


## Is ExecuteDelete a real “Bulk Delete”?

**Short answer: not quite.**

Yes, it deletes rows in bulk directly in SQL.
However, it does **not** delete entities from a list with per-row control like **[BulkDelete](https://entityframework-extensions.net/bulk-delete)** (Entity Framework Extensions).

The difference is simple:

* **`ExecuteDelete`**: “Delete all rows matching this condition.”
* **`BulkDelete`**: “Delete exactly these entities.”

```csharp
// ExecuteDelete
await context.Customers
    .Where(c => c.Age > 30)
    .ExecuteDeleteAsync();

// BulkDelete (with EF Extensions)
await context.BulkDeleteAsync(customers);
```

### What’s the practical difference?

| Question                       | `ExecuteDelete` (EF Core)             | [BulkDelete](https://entityframework-extensions.net/bulk-delete) (EF Extensions)          |
| ------------------------------ | ----------------------------------- | ----------------------------------- |
| How are rows selected?         | One LINQ rule for all matching rows | Explicit list of entities |
| Where does the data come from? | LINQ expression translated to SQL   | In-memory entity list               |
| How many rows?                 | Thousands to hundreds of thousands  | Designed for very large volumes     |
| Related entities supported?    | ❌ No                                | ✅ Yes (`IncludeGraph`)             |
| Change Tracker sync?           | ❌ No                                | ✅ Yes (With option)                |


### Performance Benchmarks

Deleting **100,000 rows**:

| Method                     | Time    | Memory   |
| -------------------------- | ------- | -------- |
| ExecuteDelete              | 200 ms  | Very low |
| BulkDelete (EF Extensions) | 1050 ms | Low      |
| SaveChanges                | 2250 ms | High     |

[Benchmark source](https://github.com/zzzprojects/learnentityframeworkcore/blob/main/benchmarks/EFCore/EFCore.Benchmarks/Benchmarks/ExecuteDeleteVsSaveChanges.cs)

> **Important:**
> For `BulkDelete` and `SaveChanges`, around **250 ms** of this time is spent **materializing entities from the database** before the delete starts.
> This is required because both approaches operate from entities, not from a LINQ rule.

**Takeaway:** `ExecuteDelete` can be **an order of magnitude faster** than the traditional tracked approach, while memory usage stays near-constant because entities are never loaded.

### Bulk Delete – Concrete Example

Suppose you need to delete **1,000,000 customers by ID** coming from a CSV import.

With `ExecuteDelete`, you would need to loop in chunks, manually build filters, or stage the data yourself.

With EF Extensions:

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

var customersToDelete = GetCustomersFromCsv();

context.BulkDelete(customersToDelete, options =>
{
    options.BatchSize = 10000;
});
```

Behind the scenes, EF Extensions sends entity keys to a temporary table and executes a high-throughput `DELETE JOIN` — something `ExecuteDelete` cannot do on its own.

### When to use which?

| Scenario                                    | Recommended                                                                                       |
| ------------------------------------------- | ------------------------------------------------------------------------------------------------- |
| Same delete rule for all matching rows      | `ExecuteDelete`                                                                                   |
| Delete rows using a LINQ filter             | `ExecuteDelete`                                                                                   |
| Delete a specific list of entities          | EF Extensions [BulkDelete](https://entityframework-extensions.net/bulk-delete)                    |
| Large-scale deletes                         | `ExecuteDelete` or EF Extensions [BulkDelete](https://entityframework-extensions.net/bulk-delete) |
| Need graph deletes, batching, or auditing   | EF Extensions [BulkDelete](https://entityframework-extensions.net/bulk-delete)                    |
| Need entity-based control                   | EF Extensions [BulkDelete](https://entityframework-extensions.net/bulk-delete)                    |

* Use **`ExecuteDelete`** when:

  * All rows can be deleted using the **same filter condition**
  * You want a **native EF Core** feature with no additional dependency
  * The dataset is **small to medium** and rule-based
  * You want **one SQL DELETE** with minimal round-trips

* Use a **[real bulk delete](https://entityframework-extensions.net/bulk-delete)** (Entity Framework Extensions) when:

  * You need to delete a **specific list of entities**
  * Deletion is driven by **external or in-memory data**
  * You’re deleting **hundreds of thousands to millions** of rows
  * You need **graph deletes**, **batching**, **auditing**, or **logging**

> **Bottom line:** `ExecuteDelete` is a fast **set-based delete** tool.  
> A **real bulk delete** is a **high-throughput entity-based pipeline** built for scale, per-row control, and advanced orchestration.  
> They **complement each other rather than compete**.

## Additional Resources – ExecuteDelete

### 📘 Recommended Reading

* [Microsoft – ExecuteDelete](https://learn.microsoft.com/en-us/ef/core/saving/execute-insert-update-delete#executedelete)
* [Entity Framework Tutorials – ExecuteDelete](https://www.entityframeworktutorial.net/efcore/execute-delete.aspx)

### 🎥 Recommended Videos

#### Entity Framework 7 – Bulk Editing

<iframe width="560" height="315" src="https://www.youtube.com/embed/A5_thTxsCjY?si=IhIioXL8AplArLl1" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

_by @CodingTutorialsAreGo ([Jasper Kent](/contributors/jasper-kent))_

In this video, Jasper introduces the new `ExecuteDelete` feature added in EF Core 7. He walks you through every step—from how deletions were traditionally performed to replacing them with the new method for both single and multiple entities. He also compares the SQL generated, explains why calling `SaveChanges` is no longer required, and shows how to retrieve the number of affected rows.

He covers everything from project setup, traditional pitfalls, and how this new method improves performance and code clarity.

**Key timestamps:**

- **[00:00 - Introduction](https://youtube.com/watch?v=A5_thTxsCjY&t=0)** – Overview of `ExecuteDelete` and `ExecuteUpdate`.
- **[00:34 - Project Setup](https://youtube.com/watch?v=A5_thTxsCjY&t=34)** – Quick explanation of the demo project.
- **[02:09 - Traditional Way to Delete](https://youtube.com/watch?v=A5_thTxsCjY&t=129)** – Shows why the old method is inefficient.
- **[07:19 - Implementing ExecuteDelete](https://youtube.com/watch?v=A5_thTxsCjY&t=439)** – Demo of the new method and SQL comparison.
- **[24:35 - Conclusion and Performance Insights](https://youtube.com/watch?v=A5_thTxsCjY&t=1475)** – Final thoughts on how game-changing these new methods are.

You can download the project source code here: [Ef7-Bulk-Actions](https://github.com/JasperKent/Ef7-Bulk-Actions)

#### Core 7 – Performance Improvements With the New ExecuteUpdate & ExecuteDelete, por Milan Jovanović

<iframe width="560" height="315" src="https://www.youtube.com/embed/VYitXAc_htI?si=SbQjx3-iJPxCjRcY" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

In the final section, Milan shows how ExecuteDelete removes multiple records in a single SQL DELETE without loading entities. He demonstrates a conditional delete, the generated SQL, and the efficiency compared to the classic deletion loop.

**Key timestamps:**

- [07:00](https://www.youtube.com/watch?v=VYitXAc_htI&t=420s) – Building the delete endpoint using ExecuteDelete
- [08:25](https://www.youtube.com/watch?v=VYitXAc_htI&t=505s) – Traditional delete pattern and why it’s inefficient
- [08:45](https://www.youtube.com/watch?v=VYitXAc_htI&t=525s) – Running ExecuteDelete with a conditional filter
- [09:30](https://www.youtube.com/watch?v=VYitXAc_htI&t=570s) – Observing the generated SQL
- [10:15](https://www.youtube.com/watch?v=VYitXAc_htI&t=615s) – SQL DELETE produced by EF Core 7
- [10:45](https://www.youtube.com/watch?v=VYitXAc_htI&t=645s) – Final explanation of the direct “range delete” behavior


#### Entity Framework 7 Makes Performing UPDATES and DELETES Easy!, por Israel Quiroz

<iframe width="560" height="315" src="https://www.youtube.com/embed/yDFwJ7JfG3s?si=MkVDh5sv3lP5Hw-1" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

Israel demonstrates how ExecuteDeleteAsync removes entities directly in the database using a filtered query—without retrieving them first. He compares the traditional 3-step delete process with the new single-roundtrip method and highlights the returned count of affected rows.

**Key timestamps:**

- [01:40](https://www.youtube.com/watch?v=yDFwJ7JfG3s&t=100s) – Traditional delete workflow (Find → Remove → SaveChanges)
- [02:00](https://www.youtube.com/watch?v=yDFwJ7JfG3s&t=120s) – Replacing the workflow with ExecuteDeleteAsync
- [02:15](https://www.youtube.com/watch?v=yDFwJ7JfG3s&t=135s) – Returned value: number of deleted rows
- [02:40](https://www.youtube.com/watch?v=yDFwJ7JfG3s&t=160s) – One database round-trip vs multiple calls
- [04:40](https://www.youtube.com/watch?v=yDFwJ7JfG3s&t=280s) – Runs without loading or tracking entities

#### 🎥 (SPANISH) Borrado y Actualizaciones Masivas – Nuevo de EF Core 7, por Felipe Gavilan

<iframe width="560" height="315" src="https://www.youtube.com/embed/o9B3pxVpPIY?si=FeEfhtc2FFGeOjv9" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

Felipe shows how ExecuteDelete removes thousands of rows directly in SQL Server without loading entities. He demonstrates a full-table delete, a filtered delete, and a performance comparison with RemoveRange.

**Key timestamps:**

- [01:47](https://www.youtube.com/watch?v=o9B3pxVpPIY&t=107s) – Full-table delete using ExecuteDelete
- [03:16](https://www.youtube.com/watch?v=o9B3pxVpPIY&t=196s) – Conditional delete using Where(...).ExecuteDelete()
- [04:32](https://www.youtube.com/watch?v=o9B3pxVpPIY&t=272s) – Verifying filtered results in SQL Server
- [08:31](https://www.youtube.com/watch?v=o9B3pxVpPIY&t=511s) – Starting stopwatch for performance comparison
- [09:31](https://www.youtube.com/watch?v=o9B3pxVpPIY&t=571s) – Final numbers: 100k deletes (8s → 0.2s)

#### Do You Know The Fastest Way To Delete Data With EF Core?, por Milan Jovanović

<iframe width="560" height="315" src="https://www.youtube.com/embed/hSSatvEOp3w?si=hF9Ha3fTBT2gg9tE" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

Milan compares three delete approaches and shows why ExecuteDeleteAsync is the most performant. He highlights SQL generation, tracking behavior, concurrency issues, and real-world cleanup scenarios.

**Key timestamps:**

- [00:45](https://www.youtube.com/watch?v=hSSatvEOp3w&t=45s) – Traditional delete workflow (Find → Remove → SaveChanges)
- [03:00](https://www.youtube.com/watch?v=hSSatvEOp3w&t=180s) – Second approach: Attach + EntityState.Deleted (concurrency issue)
- [08:32](https://www.youtube.com/watch?v=hSSatvEOp3w&t=512s) – Introducing ExecuteDeleteAsync
- [08:45](https://www.youtube.com/watch?v=hSSatvEOp3w&t=525s) – Demo: one SQL query, no tracking
- [09:15](https://www.youtube.com/watch?v=hSSatvEOp3w&t=555s) – Final conclusion: most performant delete method

## Summary & Next Steps – ExecuteDelete

`ExecuteDelete` is a powerful EF Core feature that enables fast, set-based deletes without loading entities, without Change Tracker overhead, and without calling `SaveChanges`. It is ideal for cleanup operations and uniform deletes.

**Use `ExecuteDelete` when:**

* You want to delete rows using a **single filter condition**
* You want maximum performance without loading entities into memory
* You prefer a **native EF Core** solution with no extra dependency

**Use EF Extensions `BulkDelete` when:**

* You need to delete a **specific list of entities**
* You are working with **hundreds of thousands or millions** of rows
* You need advanced features like **graph deletes**, **batching**, or **auditing**

**Next steps:**

* Explore the [EF Extensions BulkDelete](https://entityframework-extensions.net/bulk-delete) documentation
* Learn about [ExecuteUpdate](/dbset/execute-update)
