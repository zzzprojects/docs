---
title: LINQ Queries in EF Core
description: Learn how to query a database with EF Core by building, filtering, ordering, composing, and executing LINQ queries.
canonical: /querying/linq-queries
status: Published
lastmod: 2026-08-10
---

# LINQ Queries in EF Core

Entity Framework Core uses LINQ to retrieve data through entity sets exposed by a `DbContext`. A query usually starts from a `DbSet<TEntity>` property or `Set<TEntity>()`, can be composed with filters and ordering, and is then executed to return the result the application needs.

For detailed examples of individual LINQ operators, see [LINQ Methods](/querying/linq-methods).

## Query a DbSet

The most common way to query an entity is through a `DbSet<TEntity>` property on the `DbContext`:

```csharp
var products = await context.Products
    .OrderBy(product => product.ProductId)
    .ToListAsync();
```

`context.Products` is the `DbSet<Product>` used as the query source. `OrderBy` defines the result order, and `ToListAsync()` executes the query and returns the products in a list.

The example orders by ProductId so the result order is deterministic.

## Query with Set<TEntity>()

You can also access a configured entity type through `Set<TEntity>()`:

```csharp
var products = await context.Set<Product>()
    .OrderBy(product => product.ProductId)
    .ToListAsync();
```

`context.Set<Product>()` returns the configured `DbSet<Product>`, so it can be composed and executed in the same way as `context.Products`.

`Set<TEntity>()` is particularly useful in generic code or when the `DbContext` does not expose a dedicated `DbSet<Product>` property. When a clearly named property such as `context.Products` is available, it is usually the more readable option.

## Build and Execute a LINQ Query

A LINQ query can be composed before its results are requested:

```csharp
IQueryable<Product> query = context.Products
    .Where(product => product.IsActive)
    .OrderBy(product => product.Name)
    .ThenBy(product => product.ProductId);

var products = await query.ToListAsync();
```

A `DbSet<Product>` can be used as an `IQueryable<Product>`. The expression assigned to `query` remains composable, so additional operations can be added before a terminal method executes it.

This is deferred execution: building the `IQueryable` does not retrieve its results. EF Core executes the query when a terminal operation such as `ToListAsync()` is called.

In this example, `ToListAsync()` materializes the results as a `List<Product>`. LINQ operators applied to that list run in memory and are no longer part of the database query.

## Filter a Query

Use `Where` to retrieve only the entities that match one or more conditions:

```csharp
var minimumPrice = 25m;

var products = await context.Products
    .Where(product =>
        product.IsActive &&
        product.Price >= minimumPrice)
    .OrderBy(product => product.Name)
    .ThenBy(product => product.ProductId)
    .ToListAsync();
```

`Where` adds the filtering condition to the query. In this example, the query returns only active products whose price is greater than or equal to `minimumPrice`.

Because `minimumPrice` remains part of the `IQueryable` expression, the provider can apply the filter before returning the results.

You can add multiple `Where` calls before the query executes. This is useful when conditions are added progressively or apply only in certain cases.

## Order a Query

Use `OrderBy` or `OrderByDescending` to define the primary order of a query. Use `ThenBy` or `ThenByDescending` to add secondary sort keys:

```csharp
var products = await context.Products
    .OrderBy(product => product.CategoryId)
    .ThenByDescending(product => product.Price)
    .ThenBy(product => product.ProductId)
    .ToListAsync();
```

The query sorts products by `CategoryId`, then sorts products with the same `CategoryId` by `Price` from highest to lowest. `ProductId` provides a unique tie-breaker when products have the same category and price.

Methods without the `Descending` suffix use ascending order; the descending variants reverse the direction for their corresponding sort key.

Without an explicit ordering, the result order is not guaranteed. Define a complete ordering whenever the position of the results matters.

Calling another `OrderBy` or `OrderByDescending` replaces the previous primary ordering. Use `ThenBy` or `ThenByDescending` to extend it instead.

## Compose Queries Dynamically

The previous examples use filters that always apply. When values such as `onlyActive`, `minimumPrice`, and `searchTerm` are optional application inputs, build the query conditionally before executing it:

```csharp
IQueryable<Product> query = context.Products;

if (onlyActive)
{
    query = query.Where(product => product.IsActive);
}

if (price > 0)
{
    query = query.Where(product => product.Price >= price);
}

if (!string.IsNullOrWhiteSpace(searchTerm))
{
    query = query.Where(
        product => product.Name.Contains(searchTerm));
}

var products = await query
    .OrderBy(product => product.Name)
    .ThenBy(product => product.ProductId)
    .ToListAsync();
```

Each optional condition adds a filter only when it applies to the current request.

The exact translation and text-comparison behavior of `Contains` depend on the database provider and its configuration.

This pattern supports different combinations of optional filters without duplicating the query-building logic.

## Choose How to Execute a Query

Once the query is fully composed, choose an execution method based on the result the application needs:

| Goal                                | Method                   |
| ----------------------------------- | ------------------------ |
| Load all matching results           | `ToListAsync()`          |
| Return the first match    		  | `FirstAsync()`  				 |
| Return the first match or `null`    | `FirstOrDefaultAsync()`  |
| Require exactly one matching result | `SingleAsync()`          |
| Return zero or exactly one result   | `SingleOrDefaultAsync()` |
| Check whether a match exists        | `AnyAsync()`             |
| Count matching results              | `CountAsync()`           |

For example, use `AnyAsync()` when the application only needs to know whether a matching product exists:

```csharp
var hasOutOfStockProducts = await context.Products
    .AnyAsync(product => product.Stock == 0);
```

The query returns a `bool` value without materializing the matching `Product` entities.

`ToListAsync()` materializes all matching results. Methods such as `AnyAsync()` and `CountAsync()` instead return a scalar value without loading those entities.

Methods that return an entity have different contracts. `FirstOrDefaultAsync()` returns the first match or `null`; apply an explicit ordering when it matters which matching entity is returned.

Use `SingleAsync()` when exactly one matching result must exist. It throws when the query returns no results or more than one. Use `SingleOrDefaultAsync()` when no result is acceptable but multiple results still indicate an error.

For detailed behavior and additional terminal operators, see [LINQ Methods](/querying/linq-methods).

## Database Translation

EF Core passes the LINQ expression to the database provider for translation. A relational provider normally translates it into SQL.

Translation support and the generated command can vary by provider and EF Core version.

A query can be valid C# and still fail when EF Core attempts to execute it if part of the expression cannot be translated. Do not assume that every .NET method or custom expression can run inside a database query.

When custom application logic is required, first retrieve only the data needed and then apply that logic to the materialized results:

```csharp
static bool HasShortName(string name)
{
    return name.Length <= 20;
}

var productNames = await context.Products
    .Where(product => product.IsActive)
    .Select(product => product.Name)
    .ToListAsync();

var shortNames = productNames
    .Where(name => HasShortName(name))
    .ToList();
```

The operations before `ToListAsync()` remain part of the EF Core query. After the names have been materialized as a `List<string>`, the second `Where` uses LINQ to Objects and runs in memory.

Application-side processing can be appropriate when the provider cannot translate the required logic. The important distinction is whether the query is materialized before the provider has applied all translatable operations.

## Avoid Common Querying Mistakes

Avoid these common mistakes when building and executing EF Core queries.

### Materializing Too Early

Materializing a query before all database-side filters have been added causes the remaining LINQ operations to run in memory:

```csharp
var products = await context.Products
    .ToListAsync();

var activeProducts = products
    .Where(product => product.IsActive)
    .ToList();
```

In this version, all products are retrieved before the application filters the list.

Keep the filter in the EF Core query when it should be applied by the database:

```csharp
var activeProducts = await context.Products
    .Where(product => product.IsActive)
    .ToListAsync();
```

Materializing before the query is complete is not always incorrect. It can be intentional when subsequent logic must run in memory. However, complete the database-side filtering first whenever possible.

### Assuming Results Have a Default Order

Without an explicit `OrderBy`, the result order is not guaranteed. Add a complete ordering when the position of the results matters, using a unique final key when necessary to resolve ties.

### Executing a Query More Times Than Expected

An `IQueryable<TEntity>` represents a composable query, not cached results. Each terminal operation can execute a separate database query:

```csharp
IQueryable<Product> query = context.Products
    .Where(product => product.IsActive);

var count = await query.CountAsync();

var products = await query
    .OrderBy(product => product.ProductId)
    .ToListAsync();
```

`CountAsync()` executes one database query, and `ToListAsync()` executes another.

Multiple executions can be appropriate when the application needs different results. Executing an `IQueryable` once does not cache its results for later terminal operations.

## External Resources - LINQ Queries

The following videos are useful if you want to see how EF Core builds and executes LINQ queries in practice. The first demonstrates the effects of composing, materializing, and processing queries in memory. The second explains how EF Core represents LINQ expressions, how the query provider translates them, and how database results are materialized as .NET objects.

### Video 1 - IEnumerable vs IQueryable in C# and EF Core | Materialization Explained

<iframe width="560" height="315" src="https://www.youtube.com/embed/HYYEeJGi1AQ" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

Coding Professionals compares queries that materialize all rows before filtering with queries that remain composable as `IQueryable`. The demonstration uses generated SQL and debugger results to show when filtering occurs in the database, when data is loaded into memory, and how LINQ operations behave after materialization.

Although the examples use synchronous terminal methods such as `ToArray()` and `ToList()`, the query composition and materialization concepts also apply when using their asynchronous EF Core counterparts.

**Key timestamps:**

- [3:34](https://www.youtube.com/watch?v=HYYEeJGi1AQ&t=214s) — Materializing all rows before applying a filter in memory
- [4:47](https://www.youtube.com/watch?v=HYYEeJGi1AQ&t=287s) — Composing an `IQueryable` with multiple filters before execution
- [9:29](https://www.youtube.com/watch?v=HYYEeJGi1AQ&t=569s) — Applying subsequent LINQ operations in memory after `ToList()` materializes the results

### Video 2 - .NET Data Community Standup - EF Core internals: IQueryable, LINQ and the EF Core query pipeline

<iframe width="560" height="315" src="https://www.youtube.com/embed/1Ld3dtnTrMw" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

Shay Rojansky and Arthur Vickers provide an advanced explanation of the EF Core query pipeline. They show how operators applied to a `DbSet` are represented as an expression tree, how the query provider processes that expression for translation, and how EF Core generates a materializer that converts database results into .NET objects.

This session goes deeper into EF Core internals than the article requires, so the selected timestamps focus on the concepts most directly related to building, translating, and materializing LINQ queries.

The session uses EF Core 7 and early EF Core 8 examples, but the selected segments focus on the stable concepts behind query composition, translation, and materialization.

**Key timestamps:**

- [11:29](https://www.youtube.com/watch?v=1Ld3dtnTrMw&t=689s) — How EF Core captures LINQ operators so the provider can translate the query to SQL
- [16:35](https://www.youtube.com/watch?v=1Ld3dtnTrMw&t=995s) — How a composed LINQ query is represented as an expression tree
- [1:18:36](https://www.youtube.com/watch?v=1Ld3dtnTrMw&t=4716s) — How EF Core generates a materializer that converts query results into objects

## Summary

EF Core queries usually start from a `DbSet<TEntity>` property or `Set<TEntity>()` and remain composable as `IQueryable<TEntity>` until a terminal method requests the result.

Keep translatable filters and ordering in the database query, then materialize the results before applying custom application logic that must run in memory.

Choose the terminal method that matches the required result, and remember that separate terminal operations can execute separate database queries.

## Related Articles

- [LINQ Methods](/querying/linq-methods) — Learn how to use individual LINQ operators with EF Core.
- Projection _(Coming soon)_ — Learn how to return only the data a query needs.
- [Include](/querying/include) — Learn how to load related entities in an EF Core query.
- Query Tracking _(Coming soon)_ — Learn how tracking behavior affects queried entities.

## FAQ

### Does `Where` execute the query?

No. `Where` adds a filter to the `IQueryable` but does not materialize the results by itself. EF Core executes the query when its results are requested, for example by calling `ToListAsync()`.

### Is `Set<Product>()` different from `context.Products`?

Both provide a `DbSet<Product>` for the configured `Product` entity type. `context.Products` is usually more readable when the property exists, while `Set<Product>()` is useful in generic code or when the `DbContext` does not expose a dedicated property.

### Does executing the same `IQueryable` twice reuse the first result?

No. Each terminal operation can execute a separate database query. Materialize and reuse the results only when that behavior is appropriate for the application.