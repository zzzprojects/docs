---
title: EF Core Querying Data
description: Learn how to query data in Entity Framework Core using LINQ, projection, joins, related-data loading, query behaviors, tracking, split queries, and raw SQL.
canonical: /querying
status: Published
lastmod: 2026-10-06
---

# Querying Data in EF Core

Querying data in Entity Framework Core usually starts with a `DbSet` and a LINQ query, but different scenarios require different result shapes, related-data loading strategies, query behaviors, or raw SQL.

This page helps you choose the right querying article based on what data you need and how EF Core should retrieve it.

## Start with LINQ Queries

LINQ is the standard way to build database queries in EF Core.

A typical query starts from a `DbSet<TEntity>`, adds operations such as filtering, ordering, or projection, and then executes the query when the application needs the result.

For example:

```csharp
var products = await context.Products
    .Where(product => product.Price >= 50)
    .OrderBy(product => product.Name)
    .ToListAsync();
```

In this workflow:

* `context.Products` provides the queryable source.
* LINQ methods such as `Where` and `OrderBy` compose the query.
* `ToListAsync()` executes the query and materializes the results.

Start with [LINQ Queries](/querying/linq-queries) for the EF Core querying workflow, including querying a `DbSet`, using `Set<TEntity>()`, filtering, ordering, and understanding when a query is executed.

If you need a broader reference to the LINQ operators available when composing and executing queries, see [LINQ Methods](/querying/linq-methods).

## Choose an EF Core Querying Approach

Use the articles in this section based on what you need to retrieve or control.

### Build and Shape Queries

Use these articles when you need to construct a query, restrict or order the returned rows, select specific values, or combine multiple query sources.

* [LINQ Queries](/querying/linq-queries) — query a `DbSet`, filter and order data, compose queries, and understand when they execute
* [LINQ Methods](/querying/linq-methods) — explore LINQ methods for filtering, ordering, projecting, grouping, aggregating, combining, and obtaining query results
* [Projection](/querying/projection) — return only the values you need, including individual properties, anonymous types, DTOs, counts, and nested results
* [Join](/querying/join) — combine related or unrelated query sources by matching their keys

### Load Related Data

Use these articles when your application needs related entities loaded through navigation properties.

* [Include](/querying/include) — specify related navigations to load with `Include`, `ThenInclude`, filtered Include, and `AutoInclude`
* [Eager Loading](/querying/eager-loading) — load related data as part of the initial query operation
* [Explicit Loading](/querying/explicit-loading) — load a related navigation deliberately after the main entity has already been retrieved
* [Lazy Loading](/querying/lazy-loading) — load related data automatically when a navigation property is accessed

### Control Query Behavior

Use these articles when you need to control automatic filtering, tracking, query splitting, or how generated SQL is annotated.

* [Global Query Filters](/querying/global-query-filters) — configure filtering rules that EF Core applies by default to queries for an entity type
* [Query Tracking](/querying/query-tracking) — choose between tracking and no-tracking queries and control the default tracking behavior
* [Split Queries](/querying/split-queries) — choose whether certain related-data queries are executed as a single query or split into multiple database queries
* [TagWith](/querying/tagwith) — add identifying comments to generated SQL to make queries easier to trace in logs or database tools

### Use Raw SQL

Use these articles when you need to write SQL directly instead of building the operation entirely with LINQ.

* [FromSql](/querying/from-sql) — query entity types from SQL that you provide
* [SqlQuery](/querying/sql-query) — query scalar values or non-entity result types from raw SQL
* [Query Parameters](/querying/query-parameters) — pass values safely to raw SQL queries and commands
* [ExecuteSql](/querying/execute-sql) — execute SQL commands that do not return query rows

## The Main Querying Decision: What Should the Query Return?

A useful way to choose an EF Core querying approach is to start with the shape of the result your application needs.

If you need entity instances and the query can be expressed naturally with LINQ, start with a query over a `DbSet<TEntity>`.

```csharp
var products = await context.Products
    .Where(product => product.Price >= 50)
    .ToListAsync();
```

This returns `Product` entities that can then be used by the application.

If you need only selected values or a custom result shape, use projection.

Projection lets you define what the query returns, such as individual properties, anonymous types, DTOs, counts, or nested projected data.

If you need related entities through navigation properties, choose a related-data loading strategy.

If you need to combine separate query sources explicitly by matching their keys, use a join.

If you need to write SQL directly, choose a Raw SQL API based on the kind of result or command the operation requires.

## Choose How Related Data Is Loaded

When your application needs related entities through navigation properties, an important decision is when that related data should be loaded.

EF Core supports three main loading strategies:

* **Eager loading** — load related data as part of the initial query operation
* **Explicit loading** — load the main entity first and retrieve a related navigation later when the application decides it is needed
* **Lazy loading** — load related data automatically when a navigation property is accessed

Use [Eager Loading](/querying/eager-loading) when you already know which related data the application will need.

For eager loading, [Include](/querying/include) explains how to specify navigation properties with `Include` and `ThenInclude`, apply filtered Include, and configure automatic loading with `AutoInclude`.

Use [Explicit Loading](/querying/explicit-loading) when the application should decide after loading the main entity whether a particular reference or collection also needs to be retrieved.

Use [Lazy Loading](/querying/lazy-loading) when navigation access itself should trigger the loading of related data.

The loading strategy affects when related data is retrieved and can affect the number of database queries executed.

## Understand Query Behavior

Result shape and loading strategy are only part of query design. You may also need to control how EF Core filters results, tracks entities, executes related-data queries, or annotates the generated SQL.

These behaviors solve different problems and are not mutually exclusive. A query can, for example, have a global filter applied, run without tracking, use split-query behavior, and include a SQL tag at the same time.

Use [Global Query Filters](/querying/global-query-filters) when the same filtering rule should apply automatically to queries for an entity type, such as excluding soft-deleted rows or restricting data to the current tenant.

Use [Query Tracking](/querying/query-tracking) to control whether returned entity instances are tracked by the current `DbContext`. Tracking keeps entity instances associated with the context, while no-tracking queries avoid that tracking when it is not needed.

Use [Split Queries](/querying/split-queries) when a query that loads related collections needs explicit control over whether EF Core retrieves the data with a single query or multiple database queries.

Use [TagWith](/querying/tagwith) when you need to add identifying comments to generated SQL so a query is easier to recognize in logs, diagnostics, or database tools.

These options affect different aspects of query behavior and can often be combined without changing the result shape requested by the application.

## LINQ or Raw SQL?

LINQ is the standard starting point for querying data in EF Core because it lets you build queries from the model using strongly typed C# expressions.

Raw SQL is useful when you need to write the SQL directly. Common scenarios include working with existing SQL, calling stored procedures, using database-specific SQL, or expressing an operation more clearly in SQL than in LINQ.

Using raw SQL does not always mean giving up LINQ composition. `FromSql` and `SqlQuery` can provide queryable sources that additional LINQ operators can compose over when the SQL is composable and the provider can translate the resulting query.

Choose the Raw SQL API based on the kind of result the operation needs:

* [FromSql](/querying/from-sql) — start an entity query from SQL you provide
* [SqlQuery](/querying/sql-query) — return scalar values or non-entity result types from SQL
* [ExecuteSql](/querying/execute-sql) — execute SQL commands when no query rows need to be returned

When values need to be supplied to SQL, [Query Parameters](/querying/query-parameters) explains how to pass them safely with the different raw SQL APIs.

Start with LINQ when the operation can be expressed clearly through the EF Core model. Use raw SQL when writing the SQL directly is the better fit for the operation.

## Summary

Querying data in EF Core depends on the result and behavior your application needs:

* LINQ over a `DbSet<TEntity>` is the standard starting point for querying entities
* projection lets you return selected values or a custom result shape
* `Include`, eager loading, explicit loading, and lazy loading control how related entities are loaded
* joins combine related or unrelated query sources by matching keys
* global query filters, tracking, split queries, and query tags control different aspects of query behavior
* Raw SQL APIs let you query entities, return scalar or non-entity results, or execute commands directly when writing SQL is the better fit