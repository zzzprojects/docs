---
title: EF Core SaveChanges - How Data Is Persisted (Tracking, Transactions, and Pitfalls)
description: Learn what SaveChanges does in Entity Framework Core, how it uses the ChangeTracker, what happens under the hood, transaction behavior, common scenarios, performance tips, and FAQs.
canonical: /saving/save-changes
status: Published
lastmod: 2026-04-08
---

# EF Core SaveChanges

## What is SaveChanges in EF Core

`SaveChanges()` persists [tracked changes](/saving/change-tracker-how-it-works) from the current `DbContext` to the database.

At a high level, it:

- Reads the state of tracked entities from the [ChangeTracker](/saving/change-tracker) (Added / Modified / Deleted)
- Builds the corresponding database commands (INSERT/UPDATE/DELETE)
- Executes those commands against the database
- Updates entity states after a successful save
- Returns the number of state entries written to the database

If tracking is misunderstood, `SaveChanges()` can feel unpredictable because it only saves entities tracked by the [ChangeTracker](/saving/change-tracker).

Example:

```csharp
using (var context = new MyDbContext())
{
    var customer = new Customer();
    customer.Name = "ZZZ Projects";
    
    context.Customers.Add(customer);

    // Persist the change to the database
    context.SaveChanges();
}
```

What happens behind the scenes:

- `Add()` starts tracking the entity and sets its state to **Added**
- `SaveChanges()` checks the state of tracked entities
- EF Core generates an **INSERT** command
- The command is executed in the database
- The entity state becomes **Unchanged**

## TL;DR - SaveChanges

- Persists **tracked changes** from a `DbContext` to the database
- Converts entity states into INSERT/UPDATE/DELETE commands
- Executes all commands within a transaction
- Updates entity states after success (**Unchanged** / deleted entities are detached)
- Throws exceptions when saving fails (`DbUpdateException`, `DbUpdateConcurrencyException`, etc.)

## Quick Mental Model

Think of the `DbContext` as holding a **pending changes list** through the **ChangeTracker**.
`SaveChanges()` is the commit step that turns those pending changes into real database operations.

If nothing is tracked as changed, `SaveChanges()` does nothing.

## Behavior / What Happens Under the Hood

This section explains the typical internal flow EF Core follows when persisting tracked changes.

A simplified sequence looks like this:

1) **Detect changes** (unless disabled)
2) **Build commands** for inserts/updates/deletes  
3) **Order commands** to respect relationships (principal/dependent)  
4) **Execute commands** against the database provider within a transaction
5) **Accept changes** (states become `Unchanged`; deleted entities are detached)  
6) **Propagate generated values** (for example, database-generated keys)

> Exact batching and ordering details can vary depending on the provider and the operations being executed.

## Common Scenarios (Connected vs Disconnected)

These two scenarios explain most real-world SaveChanges behavior and pitfalls.

### Connected scenario

You query entities using a `DbContext`, modify them while they remain tracked, then call `SaveChanges()`.

```csharp
using (var context = new MyDbContext())
{
    var product = context.Products.First();
    product.Price += 10;

    context.SaveChanges();
}
````

* EF Core already knows what changed
* This is the simplest and most predictable approach

For a deeper walkthrough of tracked updates, see [Saving Data in Connected Scenario](/saving/save-changes-connected-entities).

### Disconnected scenario

Entities come from outside the context (DTOs, API payloads, background jobs, etc.) and are not tracked by default.

```csharp
// Example: entity comes from an API payload (disconnected)
var product = new Product { Id = 10, Name = "Keyboard", Price = 150 };

using (var context = new MyDbContext())
{
    context.Update(product);
    context.SaveChanges();
}
```

* You need `Update()` or `UpdateRange()` to update
* You need `Delete()` or `DeleteRange()` to delete
* You can also call `Attach` or `AttachRange` and modify the state




If you only want to update specific properties in a disconnected scenario, avoid marking the whole entity as modified when possible:

```csharp
var product = new Product { Id = 10 };

using (var context = new MyDbContext())
{
    context.Attach(product);
    product.Price = 150;
    context.Entry(product).Property(p => p.Price).IsModified = true;
    context.SaveChanges();
}
```

This reduces the risk of unintentionally updating columns you did not mean to change.

## Generated SQL

`SaveChanges()` typically sends multiple commands (INSERT/UPDATE/DELETE), not “one big SQL statement”. EF Core may also batch multiple commands together to reduce database roundtrips.

A practical way to inspect what EF Core executes is to enable logging during development:

```csharp
var options = new DbContextOptionsBuilder<AppDbContext>()
    .LogTo(Console.WriteLine, LogLevel.Information)
    .EnableSensitiveDataLogging()
    .Options;

using (var context = new MyDbContext(options))
{
    // ... make changes ...
    context.SaveChanges();
}
```

Typical output includes:

* Generated SQL commands
* Parameter values
* Transaction-related events

Use sensitive-data logging only in development. For more details, see the [EF Core simple logging documentation](https://learn.microsoft.com/en-us/ef/core/logging-events-diagnostics/simple-logging).

## Transaction

EF Core uses a transaction to prevent partial updates:

* If all commands succeed → commit
* If any command fails → the save fails and an exception is thrown

If you start a transaction explicitly, EF Core typically participates in it by creating a **savepoint** for database providers that support it. If `SaveChanges()` fails, EF Core rolls back to this savepoint and then throws the exception.

```csharp
using (var context = new MyDbContext())
{
    context.Database.OpenConnection();
    using var transaction = context.Database.BeginTransaction();
    
    // ... make tracked changes ...
    context.SaveChanges();

    transaction.Commit();
}
```

For more details about transaction behavior in EF Core, see the [official transactions documentation](https://learn.microsoft.com/en-us/ef/core/saving/transactions) and the general [saving data overview](https://learn.microsoft.com/en-us/ef/core/saving/) in the EF Core docs.

## Performance Characteristics / Tips

This section focuses on the most common `SaveChanges()` bottlenecks and the fixes that matter most.

`SaveChanges()` performance depends on:

* Number of tracked entities in the context
* Change detection overhead (`DetectChanges`)
* Number of commands generated
* Provider capabilities (batching, parameterization, execution strategy)

Practical tips:

* Use `AddRange` / `UpdateRange` / `RemoveRange` for convenience when working with multiple entities. A common misconception is that they improve performance over `Add` / `Update` / `Remove`, which they do not.
* Avoid calling `SaveChanges()` per entity in loops (batch changes and save once)
* Keep contexts reasonably scoped (for example, per request or unit of work)
* For set-based updates or deletes, prefer [ExecuteUpdate](/dbset/execute-update) / [ExecuteDelete](/dbset/execute-delete) when appropriate
* Be intentional with tracking when loading large amounts of data

If you need to save a large number of entities, batching work into chunks can help keep memory usage and change-tracking overhead under control:

```csharp
int batchSize = 500;

foreach (var batch in products.Chunk(batchSize))
{
    using (var context = new MyDbContext())
    {
        context.Products.AddRange(batch);
        context.SaveChanges();
    }
}
```

Chunking is useful when standard `SaveChanges()` is still acceptable but saving everything in one very large tracked graph becomes inefficient.

If you regularly process very large volumes, bulk tooling is usually the better fit.

**Bulk note (very large batches):**
If you need to persist very large batches (tens of thousands of rows), standard `SaveChanges()` can become a bottleneck. Bulk extensions (for example [Entity Framework Extensions](https://entityframework-extensions.net/bulk-savechanges) by ZZZ Projects) provide `BulkSaveChanges()`-style approaches that can be significantly faster.

## When to Use vs When NOT to Use SaveChanges

Use `SaveChanges()` when:

* You’re doing standard CRUD operations and want EF Core to generate SQL for you
* You’re working in a connected scenario (tracked entities)
* You can save changes in sensible units (per request, per service operation, etc.)

Be careful or avoid naive usage when:

* You call `SaveChanges()` repeatedly in tight loops (performance and transaction overhead)
* You handle disconnected graphs without explicit state control
* You require special transactional boundaries but don’t control transactions explicitly
* You need to persist massive volumes without batching or bulk strategy

## External Resources - SaveChanges

The following resources provide deeper insight into how EF Core persists changes, including how the ChangeTracker works internally, what `SaveChanges()` actually does, and common pitfalls that lead to unexpected updates.

They are especially useful for understanding why saving behavior is often tied to tracking, how entity states influence generated commands, and what patterns are recommended in real-world applications.

### Video 1 - How does EF Core keep track of changes?

<iframe width="560" height="315" src="https://www.youtube.com/embed/uXDYEBexlYk?si=ecJMtk1ZxKjv4JOs" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

Hubert Mijalski explains how EF Core uses the **ChangeTracker** internally, with a focus on entity states and what `SaveChanges()` actually persists.

This video provides essential context to understand why saving can produce unexpected updates, especially in disconnected scenarios or when using `Update()` incorrectly.

Key sections:

* [00:00](https://www.youtube.com/watch?v=uXDYEBexlYk) — ChangeTracker intro and entity states (Added/Unchanged/Deleted/Detached)
* [05:00](https://youtu.be/uXDYEBexlYk?si=Kmyt0fUljnvz_-8h&t=300) — `AsNoTracking()` vs `Update()` and why `Update()` can be risky or overused
* [09:00](https://youtu.be/uXDYEBexlYk?si=Kmyt0fUljnvz_-8h&t=540) — ChangeTracker scope in web apps (typical “one SaveChanges per unit of work” pattern)
* [12:00](https://youtu.be/uXDYEBexlYk?si=Kmyt0fUljnvz_-8h&t=720) — Code demo: normal UPDATE vs `Update()` (SQL logs)

### Video 2 - How SaveChanges works and 1 common mistake (EF Core / .NET 8)

<iframe width="560" height="315" src="https://www.youtube.com/embed/lehYoEhFiIM?si=clN4t9FuuBZshX5i" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

Pawel Minkina explains how `SaveChanges()` works using a simple airport analogy, then backs it up with benchmarks around batching.

This is especially useful to understand why calling `SaveChanges()` per entity is one of the most common performance mistakes, and how change detection can become a bottleneck at scale.

Key sections:

* [00:00](https://www.youtube.com/watch?v=lehYoEhFiIM) — Introduction + airport analogy (`DbContext` as the bus, `SaveChanges()` as the takeoff)
* [02:30](https://youtu.be/lehYoEhFiIM?si=AoJvqstwhO_fweP4&t=150) — Benchmark: batching vs calling `SaveChanges()` individually (≈10x improvement)
* [05:00](https://youtu.be/lehYoEhFiIM?si=AoJvqstwhO_fweP4&t=300) — Bulk run (5M items): recreating the `DbContext` every 5k to avoid memory pressure
* [07:30](https://youtu.be/lehYoEhFiIM?si=AoJvqstwhO_fweP4&t=450) — `AutoDetectChangesEnabled = false` and why it can boost performance for large saves

### Video 3 - Boost EF Core Performance: BulkInsert, BulkUpdate, BulkDelete & More (Full Guide)

<iframe width="560" height="315" src="https://www.youtube.com/embed/VG-aU7WRl5g?si=M4AVM6duUS9hs8jh" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

Anton Martyniuk walks through why `SaveChanges()` can become slow at scale, then shows bulk alternatives and when they make a measurable difference.

This is a practical companion resource if you’re saving large volumes and need predictable performance beyond standard batching.

Key sections:

* [00:00](youtube.com/watch?v=VG-aU7WRl5g&source_ve_path=MTc4NDI0) — Intro + benchmark: `SaveChanges()` with 10k records (slow due to parameter/merge constraints)
* [03:00](https://youtu.be/VG-aU7WRl5g?si=fs-P0GALQqVhcFKg&t=180) — `BulkInsert` vs `BulkInsertOptimized` (performance difference without temp table)
* [08:00](https://youtu.be/VG-aU7WRl5g?si=fs-P0GALQqVhcFKg&t=480) — `BulkUpdate` + `BulkDelete` (options like ignore/primary key, `IncludeGraph` for nested graphs)
* [12:00](https://youtu.be/VG-aU7WRl5g?si=fs-P0GALQqVhcFKg&t=720) — `BulkMerge` + `BulkSynchronize` (upsert + insert/update/delete combined)

## Summary & Next Steps

`SaveChanges()` is the standard way EF Core persists tracked changes to the database.
The deeper you understand **ChangeTracker**, **transactions**, and common failure modes, the more predictable saving becomes.

Next steps:

* [Adding Data](/saving/adding-data)
* [Modifying Data](/saving/modifying-data)
* [Deleting Data](/saving/deleting-data)


## FAQ

**Does AddRange perform a BulkInsert?**

No, this is a common misconception often found in articles and AI-generated content.

Using `Add` or `AddRange` does not change how entities are inserted. Only the database provider affects how inserts are executed. For example, SQL Server can use multiple row inserts, while most other providers execute multiple INSERT statements in the same command.

Since `DetectChanges` is now called when `SaveChanges()` is executed (unlike EF6), both methods usually have very similar performance, as shown in this [article](https://www.entityframeworktutorial.net/efcore/4-best-ways-to-do-bulk-inserts-in-entity-framework.aspx).

Even Microsoft clearly states in its own [documentation](https://learn.microsoft.com/en-us/ef/core/change-tracking/miscellaneous#addrange-updaterange-attachrange-and-removerange) that there is no significant performance benefit

**What does SaveChanges return in EF Core?**

It returns the number of state entries written to the database.

**Does SaveChanges always run inside a transaction?**

Yes, this is the default behavior.

**Why do I get DbUpdateException?**

Usually because the database rejected a command (constraint violation, invalid data, SQL error, etc.).

**Why do I get DbUpdateConcurrencyException?**

Because an expected UPDATE/DELETE affected 0 rows (data changed since it was loaded or concurrency tokens didn’t match).

**Why is SaveChanges slow when I track many entities?**

Tracking and change detection add overhead. Large graphs and long-lived contexts can increase that overhead significantly.