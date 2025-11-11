---
title: EF Core 10 – Top New Features Every Developer Should Know
description: Discover EF Core 10’s best new features: LeftJoin & RightJoin, JSON columns, smarter ExecuteUpdateAsync, and flexible query filters. Learn what’s new!
canonical: /efcore/efcore-10-what-is-new
status: Published
lastmod: 2025-11-11
---

# EF Core 10 – What’s New?

EF Core 10 was officially released on **2025-11-11**

This release brings plenty of cool new features — some that you’ll use right away, and others that you’ll probably just smile at and move on.

From better LINQ support to smarter SQL generation and a few long-awaited fixes, this version makes EF Core even more enjoyable to work with.

In this video by **Israel**, let’s discover together what EF Core 10 brought us:

<iframe width="560" height="315" src="https://www.youtube.com/embed/QGTxzosSncY?si=ipbPMgYFjGDi4NUT" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

**🕒 Key timestamps in the video:**

* [00:00 - Intro](https://www.youtube.com/watch?v=QGTxzosSncY)
* [00:27 - Azure SQL and SQL Server Updates](https://www.youtube.com/watch?v=QGTxzosSncY&t=27s)
* [02:39 - Azure Cosmos DB for NoSQL](https://www.youtube.com/watch?v=QGTxzosSncY&t=159s)
* [03:14 - JSON data type support!](https://www.youtube.com/watch?v=QGTxzosSncY&t=194s)
* [05:05 - LINQ and SQL translation updates](https://www.youtube.com/watch?v=QGTxzosSncY&t=305s)
* [05:49 - 2 NEW LINQ METHODS LeftJoin and RightJoin](https://www.youtube.com/watch?v=QGTxzosSncY&t=349s)
* [07:08 - ExecuteUpdate integration with JSON data type](https://www.youtube.com/watch?v=QGTxzosSncY&t=428s)
* [07:47 - ExecuteUpdateAsync update to allow non-expression lambdas!](https://www.youtube.com/watch?v=QGTxzosSncY&t=467s)
* [08:37 - Updates To Named Query Filters](https://www.youtube.com/watch?v=QGTxzosSncY&t=517s)

You can also find everything in the official [What's New page](https://learn.microsoft.com/en-us/ef/core/what-is-new/ef-core-10.0/whatsnew).

In this article, we’ll focus only on the **three new features developers are most likely to use** — the ones that can truly make a difference in your day-to-day work.

---

## 2 NEW LINQ METHODS: LeftJoin and RightJoin

EF Core 10 introduces two new LINQ extension methods: `LeftJoin` and `RightJoin`.

These methods let you explicitly choose whether to generate a **LEFT JOIN** or **RIGHT JOIN** in the SQL command. This makes it easier to return results from **two tables even if they aren’t related** in your model.

Here’s a simple example using `LeftJoin` to show how each parameter works:

```csharp
var query = context.Students // Choose the first table (DbSet)
    .LeftJoin(
        context.Departments, // Choose the second table (DbSet)
        student => student.DepartmentID, // Key from the first table
        department => department.ID, // Key from the second table
        (student, department) => new // Result created from both tables
        { 
            student.FirstName,
            student.LastName,
            Department = department.Name ?? "[NONE]"
        });
```

You can also watch this great video by **Israel** explaining everything in more detail:

<iframe width="560" height="315" src="https://www.youtube.com/embed/0kvFnB3_a24?si=gHOA7asexYX_yZN8" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

---

## ExecuteUpdateAsync Update to Allow Non-Expression Lambdas

Starting with EF Core 10, you can now use **non-expression lambdas** inside `ExecuteUpdateAsync`.

In short, this means you can finally include **custom logic directly inside the lambda body**, making your update operations more flexible and readable.

```csharp
bool nameChanged = true; // Local variable, not a column

await context.Blogs.ExecuteUpdateAsync(s =>
{
    s.SetProperty(b => b.Views, 8);
    if (nameChanged)
    {
        s.SetProperty(b => b.Name, "foo");
    }
});
```

---

## Updates to Named Query Filters

Query filters are filters that are automatically applied to all queries for an entity type.

EF Core 10 finally makes global query filters much more flexible!

Before this version, you could only choose to **use all filters or none of them**. For example, if you had both an `IsDeleted` and a `TenantID` filter, you couldn’t disable just one — it was all or nothing.

With EF Core 10, you can now **assign names to your query filters** and manage them individually. This allows you to easily control which filters are applied by using the `IgnoreQueryFilters` method to skip specific ones.

We recommend using an **enum** or a **named variable** when assigning filter names — hardcoding strings is always the worst decision.

Here’s a simple example:

```csharp
enum QueryFilter
{
    SoftDeletion,
    Tenant
}

modelBuilder.Entity<Blog>()
    .HasQueryFilter(QueryFilter.SoftDeletion.ToString(), b => !b.IsDeleted)
    .HasQueryFilter(QueryFilter.Tenant.ToString(), b => b.TenantId == tenantId);
```

And if you ever want to ignore only one of them:

```csharp
var allBlogs = await context.Blogs
    .IgnoreQueryFilters([QueryFilter.SoftDeletion.ToString()])
    .ToListAsync();
```

This new feature gives you a lot more control over how filters are applied — especially in larger applications where you might have multiple layers of filtering.

> 💡 **Recommendation:** Never hardcode filter names as strings. Using an **enum** (like in the example) or a named variable helps keep your code clean, prevents typos, and makes refactoring safer.

---

### JSON Columns

EF Core 10 now lets you **map complex types directly to JSON columns** — a major improvement for working with structured data in databases like **SQL Server 2025**.

You can now:

* Use `.ComplexProperty(...).ToJson()` to store complex types as JSON
* Query and filter nested JSON properties in LINQ
* Create indexes on JSON paths for faster reads
* Use `ExecuteUpdateAsync` to update JSON properties in bulk

Here’s a quick example:

```csharp
modelBuilder.Entity<User>(entity =>
{
    entity.ComplexProperty(e => e.Profile, c => c.ToJson());
    entity.HasIndex(e => e.Profile.Email);
});
```

And you can query nested JSON values directly:

```csharp
var users = await context.Users
    .Where(u => u.Profile.Email.EndsWith("@example.com"))
    .ToListAsync();
```

This feature removes the need for manual serialization, improves performance with native JSON indexing, and makes it easier to evolve your model without schema changes.

👉 **Learn more:** [Unlock JSON Columns with EF Core 10 (Dev Skills Unlock)](https://www.devskillsunlock.com/blog/json-columns-outbox-pattern-efcore10-sql-server)

---

## Conclusion

EF Core 10 continues to move in the right direction — improving performance, adding flexibility, and making everyday development just a little smoother.

We covered what are probably the **three most practical and widely used features**:

* The new `LeftJoin` and `RightJoin` methods for more control over your LINQ joins
* The `ExecuteUpdateAsync` update that finally allows custom logic in your updates
* The new **Named Query Filters**, giving you finer control over global filters
* And the new **JSON Columns** support, letting you map and query complex types directly in JSON

Of course, there are many other additions in this release, but these three are the ones most developers will actually benefit from day to day.

If you want to explore everything EF Core 10 offers, check out the [official “What’s New” page](https://learn.microsoft.com/en-us/ef/core/what-is-new/ef-core-10.0/whatsnew).
