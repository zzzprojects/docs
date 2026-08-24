---
title: Split Queries in EF Core
description: Learn when to use split queries in EF Core, including AsSplitQuery, AsSingleQuery, global query splitting behavior, and single vs. split query trade-offs.
canonical: /querying/split-queries
status: Published
lastmod: 2026-08-24
---

# Split Queries in EF Core

EF Core uses single-query behavior by default when loading related data. Use `AsSplitQuery()` when you want a query that loads related collections to execute as multiple database queries instead.

## Using AsSplitQuery

Use `AsSplitQuery()` to enable split-query behavior for a specific query.

```csharp
var blogs = await context.Blogs
    .Include(blog => blog.Posts)
    .Include(blog => blog.Contributors)
    .AsSplitQuery()
    .ToListAsync();
```

This query loads each `Blog` together with its `Posts` and `Contributors` collections. See [Include](/querying/include) for more about loading related entities through navigation properties.

For this query shape, EF Core executes three database commands: one for the blogs, one for the posts, and one for the contributors.

The result is still a `List<Blog>` with the related `Posts` and `Contributors` loaded.

This is most useful when loading multiple sibling collections would otherwise produce a large joined result.

`AsSplitQuery()` configures the query; it does not execute it. `ToListAsync()` executes the query and materializes the results. See [LINQ Queries](/querying/linq-queries) for more about query composition and execution.

## Why Use a Split Query?

Without `AsSplitQuery()`, EF Core uses single-query behavior by default:

```csharp
var blogs = await context.Blogs
    .Include(blog => blog.Posts)
    .Include(blog => blog.Contributors)
    .ToListAsync();
```

When EF Core detects a query that loads multiple related collections and no query-splitting behavior has been selected globally or for the query, it can also log a `MultipleCollectionIncludeWarning`.

A single query is not inherently a problem. However, when multiple collection navigations are included at the same level, the JOINs used to load them can multiply the number of rows returned by the database.

For example, `Posts` and `Contributors` are both collection navigations of `Blog`:

```text
Blog
├── Posts
└── Contributors
```

If one blog has 10 posts and 10 contributors, a single relational query can return 100 rows for that blog because each post is combined with each contributor.

This is commonly called **cartesian explosion**. As more sibling collections are included, the amount of duplicated data returned by the database can grow quickly.

`AsSplitQuery()` avoids this sibling collection cross product by loading the related collections through separate queries.

Nested collection navigations are different. For example:

```csharp
var blogs = await context.Blogs
    .Include(blog => blog.Posts)
        .ThenInclude(post => post.Comments)
    .ToListAsync();
```

Here, `Comments` is a collection navigation of `Post`, not another collection navigation of `Blog`:

```text
Blog
└── Posts
    └── Comments
```

This query does not produce the same sibling collection cross product as loading `Posts` and `Contributors` at the same level. See [Include](/querying/include) for more about loading nested related data with `ThenInclude`.

## Using AsSingleQuery

Use `AsSingleQuery()` when you want a specific query to use single-query behavior explicitly.

```csharp
var blogs = await context.Blogs
    .Include(blog => blog.Posts)
    .Include(blog => blog.Contributors)
    .AsSingleQuery()
    .ToListAsync();
```

This query requests the same `Blog`, `Posts`, and `Contributors` data as the previous example, but EF Core executes it using a single database query.

For this query shape, `AsSingleQuery()` executes one database command instead of the three commands produced by the split-query example above.

The result is still a `List<Blog>` with both related collections loaded. `AsSingleQuery()` changes the query execution behavior; it does not change the expected object graph returned to the application.

## Configure Split Queries Globally

Use `UseQuerySplittingBehavior()` when you want split-query behavior to be the default for queries created by a `DbContext`.

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
{
    optionsBuilder.UseSqlServer(
        connectionString,
        sqlServerOptions => sqlServerOptions.UseQuerySplittingBehavior(
            QuerySplittingBehavior.SplitQuery));
}
```

With `QuerySplittingBehavior.SplitQuery` configured, EF Core uses split-query behavior as the default for queries that load related collections.

You can still override the default for an individual query:

```csharp
var blogs = await context.Blogs
    .Include(blog => blog.Posts)
    .Include(blog => blog.Contributors)
    .AsSingleQuery()
    .ToListAsync();
```

In this case, `AsSingleQuery()` overrides the globally configured `SplitQuery` behavior for this query.

The two available `QuerySplittingBehavior` values are:

* `SingleQuery` — load related collections using single-query behavior.
* `SplitQuery` — load related collections using split-query behavior.

## Single vs. Split Query Trade-offs

Single and split queries load the same related data using different execution strategies. Neither approach is always better.

| Behavior     | Main benefit                                      | Main trade-off                                                                       |
| ------------ | ------------------------------------------------- | ------------------------------------------------------------------------------------ |
| Single query | Uses one database query for the related-data load | Sibling collection JOINs can produce cartesian explosion                             |
| Split query  | Avoids the sibling collection cross product       | Uses multiple database queries, adding round trips and possible consistency concerns |

Split queries can also introduce data consistency concerns. Because the result is assembled from multiple database queries, data that changes between those queries can lead to an inconsistent combined result. A transaction with an appropriate isolation level can provide stronger consistency guarantees, but it also introduces its own trade-offs. See [Transactions](/saving/transactions) for more about using transactions in EF Core.

Additional queries can increase latency when the database is remote. Depending on the database provider, results from earlier queries may also need to be buffered before later queries execute.

When reference navigations are loaded together with collection navigations, the split queries can still contain JOINs for those references.

Single queries can also duplicate data from the principal entity across rows. This is usually insignificant, but it can matter when the principal contains large columns. When those columns are not needed, [Projection](/querying/projection) can reduce the data selected by the query.

## When Should You Use Split Queries?

Consider split queries when:

* a query loads multiple sibling collections;
* the resulting JOINs would produce a large cross product;
* avoiding a large cross product is more important than minimizing database round trips.

Prefer single-query behavior when:

* the joined result is reasonably small;
* minimizing database round trips is important;
* obtaining the related data through one database query is more important for consistency.

Do not assume that split queries are always faster. Measure the actual query behavior and choose the strategy that fits the query shape and workload.

## External Resources - Split Queries

The following videos are useful if you want to see why split queries can help with certain query shapes and how EF Core changes the SQL execution strategy when `AsSplitQuery()` is applied. The first focuses on cartesian explosion, trade-offs, and measured performance differences. The second provides a practical walkthrough of `AsSplitQuery()`, the generated SQL, global query-splitting configuration, and per-query override with `AsSingleQuery()`. The third adds a real-world performance diagnosis, including EF Core's multiple-collection warning, a problematic projection without `Include`, and the measured result after applying `AsSplitQuery()`.

### Video 1 - Are You Accidentally Crippling Your EF Core Queries?

<iframe width="560" height="315" src="https://www.youtube.com/embed/G7VR14Gt2O0" title="Are You Accidentally Crippling Your EF Core Queries?" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

Milan Jovanović demonstrates how loading sibling collections can produce a cartesian product, then applies `AsSplitQuery()` and inspects the separate SQL commands sent to the database. He also discusses the additional round trips and consistency trade-offs of split queries and uses BenchmarkDotNet to compare scenarios where splitting a query helps and where it does not.

**Key timestamps:**

* [12:24](https://www.youtube.com/watch?v=G7VR14Gt2O0&t=744s) — Showing `Tasks` and `SalaryPayments` as sibling collections and explaining the resulting cartesian product
* [13:21](https://www.youtube.com/watch?v=G7VR14Gt2O0&t=801s) — Inspecting the separate SQL commands executed after applying `AsSplitQuery()`
* [14:06](https://www.youtube.com/watch?v=G7VR14Gt2O0&t=846s) — Explaining why query splitting is not a universal solution, including additional round trips and consistency concerns
* [16:14](https://www.youtube.com/watch?v=G7VR14Gt2O0&t=974s) — Comparing single and split queries with BenchmarkDotNet, including one scenario where split is slower and another where it is substantially faster

### Video 2 - One Trick To Improve EF Core Performance Using Query Splitting

<iframe width="560" height="315" src="https://www.youtube.com/embed/GY7QwSFeVBQ" title="One Trick To Improve EF Core Performance Using Query Splitting" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

Milan Jovanović provides a focused walkthrough of query splitting in EF Core. He applies `AsSplitQuery()`, inspects the separate SQL statements generated for the query, discusses round-trip and consistency trade-offs, and shows how to configure split-query behavior globally and override it for an individual query with `AsSingleQuery()`.

**Key timestamps:**

* [4:42](https://www.youtube.com/watch?v=GY7QwSFeVBQ&t=282s) — Inspecting the separate SQL queries generated after applying `AsSplitQuery()`
* [6:19](https://www.youtube.com/watch?v=GY7QwSFeVBQ&t=379s) — Explaining multiple database round trips and the risk of inconsistent results between split queries
* [8:33](https://www.youtube.com/watch?v=GY7QwSFeVBQ&t=513s) — Configuring split-query behavior globally with `UseQuerySplittingBehavior()`
* [9:15](https://www.youtube.com/watch?v=GY7QwSFeVBQ&t=555s) — Overriding the global split-query behavior for one query with `AsSingleQuery()`

### Video 3 - Episode 013 - The Tech Vault - EF Core Cartesian Explosions: Fixing a 13-Second Query

<iframe width="560" height="315" src="https://www.youtube.com/embed/OkQQq7mHfgc" title="Episode 013 - The Tech Vault - EF Core Cartesian Explosions: Fixing a 13-Second Query" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

Mitchel Sellers walks through a real-world EF Core performance problem caused by a query shape that loads multiple sibling collections. He shows how sibling collection joins multiply rows, inspects EF Core's warning about multiple collection navigations without a configured splitting behavior, and demonstrates a problematic projection that does not use `Include`. After applying `AsSplitQuery()`, he compares the request before and after the change and closes with guidance on when split queries are useful and which trade-offs to consider.

**Key timestamps:**

* [2:35](https://www.youtube.com/watch?v=OkQQq7mHfgc&t=155s) — Visualizing how sibling collection joins create a cartesian product, with 10 posts × 12 contributors producing 120 rows
* [6:02](https://www.youtube.com/watch?v=OkQQq7mHfgc&t=362s) — Showing a problematic projection with multiple related collections and applying `AsSplitQuery()` even though the query does not use `Include`
* [8:24](https://www.youtube.com/watch?v=OkQQq7mHfgc&t=504s) — Showing the result in this specific case after applying `AsSplitQuery()`, with request time dropping from 13,689 ms to 664 ms
* [9:39](https://www.youtube.com/watch?v=OkQQq7mHfgc&t=579s) — Summarizing when split queries are useful and the main trade-offs, including additional round trips, buffering, consistency concerns, and repeated reference joins

## Summary

EF Core uses single-query behavior by default when loading related data.

* Use `AsSplitQuery()` to execute a specific query using split-query behavior.
* Use `AsSingleQuery()` to explicitly use single-query behavior.
* Use `UseQuerySplittingBehavior()` to configure the default behavior for a `DbContext`.
* Use the per-query operators to override the configured default when needed.
* Consider split queries when sibling collection JOINs would otherwise produce a large cross product.
* Consider the additional database queries, round trips, and consistency trade-offs before choosing split-query behavior.

Neither strategy is always better. Choose based on the query shape and workload.

## Related Articles

* [Include](/querying/include) — Learn how to load related entities through navigation properties.
* [LINQ Queries](/querying/linq-queries) — Learn how to build, compose, and execute EF Core queries.
* [Projection](/querying/projection) — Learn how to select only the data a query needs.

## FAQ

### Does EF Core use split queries by default?

No. EF Core uses single-query behavior by default unless a different query-splitting behavior is configured.

### Does `AsSplitQuery()` always execute the same number of queries?

No. The number of database queries depends on the query shape and the related collections being loaded.

### Are split queries always faster than single queries?

No. Split queries can avoid cartesian explosion, but they also execute multiple database queries and can introduce additional round trips and data consistency concerns.

### Can `AsSingleQuery()` override a global split-query configuration?

Yes. If `QuerySplittingBehavior.SplitQuery` is configured as the default, `AsSingleQuery()` can override that behavior for an individual query.