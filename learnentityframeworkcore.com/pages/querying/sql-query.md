---
title: SqlQuery in EF Core
description: Learn how to use SqlQuery and SqlQueryRaw in EF Core to query scalar values and non-entity types with raw SQL, return DTOs, and compose LINQ over SQL queries.
canonical: /querying/sql-query
status: Published
lastmod: 2026-09-07
---

# SqlQuery in EF Core

`SqlQuery` lets you query scalar values and non-entity result types using raw SQL without starting from a `DbSet`.

Use `SqlQuery` when raw SQL should return scalar values or mappable CLR types that are not part of the EF Core model.

For raw SQL queries that return mapped entities, use [`FromSql`](/querying/from-sql) instead. For SQL commands that do not return rows, use `ExecuteSql`.

## Use `SqlQuery`

Use `SqlQuery<TResult>` when a raw SQL query should return values that EF Core can materialize as `TResult`.

```csharp
var productNames = await context.Database
    .SqlQuery<string>($"SELECT Name FROM Products")
    .ToListAsync();
```

In this example:

* `context.Database` provides access to relational database APIs that are not rooted in a `DbSet`.
* `SqlQuery<string>(...)` creates a query whose result type is `string`.
* the SQL returns one `Name` value for each row;
* `ToListAsync()` executes the query and returns a `List<string>`.

`SqlQuery` returns an `IQueryable<TResult>` and does not execute the SQL immediately. The database query runs when the application requests the results through an operation such as `ToListAsync()`.

The type specified by `TResult` determines the result shape EF Core should materialize. It can be a scalar type such as `int`, `string`, or `decimal`, or a mappable CLR type that represents multiple columns without being part of the EF Core model.

## Pass Parameters with `SqlQuery`

Use interpolated values with `SqlQuery` when the SQL query needs parameters.

```csharp
var minimumProductId = 2;

var productNames = await context.Database
    .SqlQuery<string>($"SELECT Name FROM Products WHERE ProductId >= {minimumProductId}")
    .ToListAsync();
```

Although the SQL is written with C# string interpolation, EF Core does not insert the value of `minimumProductId` directly into the SQL text.

Instead, EF Core converts the interpolated value into a database parameter and sends it separately from the SQL statement.

This means values passed through `SqlQuery` are parameterized automatically.

Use this pattern when the parts that vary are data values, such as identifiers, dates, names, or numeric values.

When part of the SQL text itself must be constructed dynamically, use `SqlQueryRaw` instead.

For a broader explanation of parameterization across EF Core raw SQL APIs, see **Query Parameters in EF Core** *(Coming soon)*.

## Use `SqlQueryRaw`

Use `SqlQueryRaw<TResult>` when part of the SQL text must be constructed dynamically and cannot be represented as a database parameter.

For example, database parameters can represent values, but they cannot represent schema elements such as column names.

```csharp
var columnName = "Name";
var columnValue = "Laptop";

var sql = $"SELECT {columnName} FROM Products WHERE Name = {{0}}";

var values = await context.Database
    .SqlQueryRaw<string>(sql, columnValue)
    .ToListAsync();
```

In this example, `columnName` becomes part of the SQL text because a column name cannot be parameterized.

`columnValue` is handled differently. The `{0}` placeholder and the additional argument passed to `SqlQueryRaw` cause EF Core to send that value as a database parameter.

`SqlQueryRaw` can also receive an explicit `DbParameter` when more control over the database parameter is required.

`SqlQueryRaw` is useful for dynamic SQL, but content inserted directly into the SQL string is not parameterized automatically.

Never insert untrusted user input directly into the SQL text. Dynamic SQL fragments such as column names should come from a trusted source or be carefully validated before the query is executed.

Starting with EF Core 10, EF Core includes an analyzer that warns about string concatenation performed directly inside raw SQL method invocations. Trusted or properly validated dynamic SQL fragments can still be used intentionally.

When only data values need to vary, prefer `SqlQuery`, which parameterizes interpolated values automatically.

## Query Scalar Values

`SqlQuery` can return a single scalar value as well as a sequence of scalar values.

The first example in this article returned several `string` values as a `List<string>`. A scalar query can also return a single row containing a single value.

For example, the following query returns the number of products in the database:

```csharp
var productCount = await context.Database
    .SqlQuery<int>($"SELECT COUNT(*) AS Value FROM Products")
    .SingleAsync();
```

Because EF Core needs to compose over the scalar SQL result to execute `SingleAsync()`, the output column is aliased as `Value` so it can be referenced from the generated SQL.

`SqlQuery<int>` specifies that EF Core should materialize the returned value as an `int`.

Because `COUNT(*)` returns one row containing one value, `SingleAsync()` executes the query and returns that value directly.

In this case, the result shape is an `int`, rather than a collection or an entity.

## Query a DTO with `SqlQuery`

`SqlQuery` is not limited to scalar values. It can also materialize multiple columns into a CLR type that is not part of the EF Core model.

For example, consider the following DTO:

```csharp
public class ProductSummary
{
    public int ProductId { get; set; }
    public string Name { get; set; } = null!;
    public decimal Price { get; set; }
}
```

You can return `ProductSummary` objects directly from a raw SQL query:

```csharp
var products = await context.Database
    .SqlQuery<ProductSummary>(
        $"""
        SELECT ProductId, Name, Price
        FROM Products
        """)
    .ToListAsync();
```

`SqlQuery<ProductSummary>` tells EF Core to materialize each row of the result set as a `ProductSummary`.

The result is a `List<ProductSummary>` containing the three values selected by the SQL query for each returned row.

`ProductSummary` does not need to be registered as an entity in the EF Core model and does not need to correspond to a database table.

The result type only needs to be mappable from the columns returned by the query. This makes `SqlQuery` useful when raw SQL should return a custom result shape, such as selected columns or data combined from multiple tables, without creating an entity type for that result.

When the same custom result shape can be expressed with LINQ instead of raw SQL, see [Projection in EF Core](/querying/projection).

Unmapped result types used with `SqlQuery` are not regular EF Core entities. They do not have entity keys or relationships defined in the model.

## Compose LINQ over `SqlQuery`

`SqlQuery` returns an `IQueryable<TResult>`, so you can compose LINQ operators over the raw SQL query before it is executed.

For example, the following query returns product IDs greater than 10:

```csharp
var productIds = await context.Database
    .SqlQuery<int>($"SELECT ProductId AS Value FROM Products")
    .Where(id => id > 10)
    .OrderBy(id => id)
    .ToListAsync();
```

`SqlQuery<int>(...)` provides the starting SQL query. `Where(...)` adds filtering, `OrderBy(...)` adds sorting, and `ToListAsync()` executes the composed query.

EF Core treats the SQL supplied to `SqlQuery` as a subquery and composes the LINQ operators over it in the database.

When EF Core composes over a scalar `SqlQuery` result and needs to reference its output column, that column must be named `Value`.

That is why the query uses:

```sql
SELECT ProductId AS Value FROM Products
```

The `Value` alias is not required simply because `SqlQuery<int>` returns a scalar value. It is required when EF Core needs to reference that scalar output while composing additional SQL over the query.

The supplied SQL must also be composable, meaning it must be valid when EF Core uses it as a subquery. Exact SQL syntax and restrictions can vary by database provider.

For more about building, composing, and executing EF Core queries, see [LINQ Queries in EF Core](/querying/linq-queries).

## Requirements and Limitations

`SqlQuery<TResult>` can return scalar values or mappable CLR types that are not part of the EF Core model, but the SQL result must be compatible with the specified `TResult`.

### Scalar Type Mapping

For scalar results, the database provider must support the CLR type being requested.

For example, a query using `SqlQuery<int>` requires the provider to be able to map the returned database value to `int`.

If a scalar CLR type is not supported by the provider by default, you can configure additional scalar mapping with `DefaultTypeMapping<TScalar>()` through pre-convention configuration.

### Non-Entity Result Types

For non-entity result types such as `ProductSummary`, EF Core must be able to populate the CLR type from the columns returned by the SQL query.

The CLR type must provide a mappable member for each value returned in the result set.

The result type does not need to map to a table and does not need to be registered as an entity in the EF Core model.

It can represent a custom result shape, including a subset of columns or values returned from a join across multiple tables.

`SqlQuery` can use common mapping patterns supported by EF Core for unmapped CLR types, including parameterized constructors and mapping attributes.

However, an unmapped result type used with `SqlQuery` is not an entity type. It does not have an entity key and cannot define relationships to other types through the EF Core model.

Returning unmapped CLR types through `SqlQuery` was introduced in EF Core 8. Scalar raw SQL queries were supported earlier.

### Composable SQL

LINQ composition requires the SQL supplied to `SqlQuery` to be valid when EF Core uses it as a subquery.

SQL that is valid as a standalone command is not necessarily valid when embedded inside another query.

Exact composition restrictions depend on the relational database provider.

### Provider-Specific SQL

`SqlQuery` executes the SQL you provide, so the SQL syntax itself is database-specific.

Identifier quoting, functions, data types, and other SQL features can differ between SQL Server, SQLite, PostgreSQL, and other relational providers.

The C# API remains the same, but the SQL string must be valid for the database provider used by the application.

## External Resources - SqlQuery

The following videos provide practical demonstrations of `SqlQuery` and related raw SQL query patterns in EF Core. Together, they show scalar and unmapped results, automatic parameterization, LINQ composition, `SqlQueryRaw`, and the EF Core 8 support for materializing custom CLR types without registering them as entities in the model.

### Video 1 - EF Core + Stored Procedures: The Right Way to Call Legacy SQL

<iframe width="560" height="315" src="https://www.youtube.com/embed/hXfHWvezBoA" title="EF Core + Stored Procedures: The Right Way to Call Legacy SQL" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

Milan Jovanović demonstrates several practical uses of `SqlQuery<T>` with PostgreSQL, including scalar results, automatic parameterization through `FormattableString`, and mapping a multi-column result into a DTO with `ToListAsync()`. Some DTO column-naming details shown in the video are PostgreSQL-specific and should not be treated as universal EF Core behavior.

**Key timestamps:**

* [**2:17**](https://www.youtube.com/watch?v=hXfHWvezBoA&t=137s) — Uses `Database.SqlQuery<int>` to return a scalar result
* [**3:37**](https://www.youtube.com/watch?v=hXfHWvezBoA&t=217s) — Explains how `FormattableString` converts interpolated values into SQL parameters
* [**5:30**](https://www.youtube.com/watch?v=hXfHWvezBoA&t=330s) — Moves from a scalar result to mapping a multi-column result into a DTO
* [**6:48**](https://www.youtube.com/watch?v=hXfHWvezBoA&t=408s) — Materializes the DTO results with `ToListAsync()`

The strongest contribution of this video is its practical progression from scalar `SqlQuery<T>` results to parameterized DTO materialization.

### Video 2 - Everything You Need To Know About EF Core 8 Raw SQL Queries

<iframe width="560" height="315" src="https://www.youtube.com/embed/tBSWhHaFB-c" title="Everything You Need To Know About EF Core 8 Raw SQL Queries" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

Milan Jovanović provides a broader walkthrough of the raw SQL query APIs introduced around EF Core 8, showing `SqlQuery<T>` with unmapped result types, automatic parameterization, LINQ composition over the returned `IQueryable`, and `SqlQueryRaw` for dynamically constructed SQL.

The video was recorded while EF Core 8 was still in preview, so its performance comparisons with Dapper should be treated as historical context rather than current EF Core 10 guidance. The selected API patterns remain directly relevant.

**Key timestamps:**

* [**0:48**](https://www.youtube.com/watch?v=tBSWhHaFB-c&t=48s) — Introduces `SqlQuery<T>` for an unmapped result type
* [**1:46**](https://www.youtube.com/watch?v=tBSWhHaFB-c&t=106s) — Explains how interpolated values are converted into SQL parameters through `FormattableString`
* [**3:42**](https://www.youtube.com/watch?v=tBSWhHaFB-c&t=222s) — Composes LINQ over `SqlQuery` and shows the additional `Where(...)` condition in the SQL sent to the database
* [**4:38**](https://www.youtube.com/watch?v=tBSWhHaFB-c&t=278s) — Introduces `SqlQueryRaw` for dynamically constructed SQL and explains how its parameters are handled

This video is particularly useful because it complements the article's `SqlQueryRaw` and LINQ composition sections with direct demonstrations of both behaviors.

### Video 3 - EF 8 new feature: Raw SQL queries for unmapped types

<iframe width="560" height="315" src="https://www.youtube.com/embed/n4fCG1T8ny8" title="EF 8 new feature: Raw SQL queries for unmapped types" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

Saeed Esmaeilinejad focuses specifically on the EF Core 8 capability that allows raw SQL results to be materialized into CLR types that are not registered as entities in the EF Core model. He contrasts this with the older keyless-entity approach and demonstrates `Database.SqlQuery<T>` mapping the query output directly into an unmapped result type.

The demo uses EF Core 8 RC1. Although it predates the final EF Core 8 release, the selected `SqlQuery<T>` pattern for unmapped CLR results remains applicable in current EF Core.

**Key timestamps:**

* [**7:46**](https://www.youtube.com/watch?v=n4fCG1T8ny8&t=466s) — Explains that an unmapped raw SQL result type does not need keyless-entity configuration for this scenario
* [**8:26**](https://www.youtube.com/watch?v=n4fCG1T8ny8&t=506s) — Introduces `Database.SqlQuery<T>` for mapping the raw SQL result
* [**9:07**](https://www.youtube.com/watch?v=n4fCG1T8ny8&t=547s) — Executes the query and maps the returned rows into the unmapped CLR type
* [**9:42**](https://www.youtube.com/watch?v=n4fCG1T8ny8&t=582s) — Highlights that the result type does not need additional entity-model configuration

The strongest contribution of this video is the focused comparison between the older keyless-entity approach and the simpler unmapped-result workflow available through `SqlQuery<T>`.

## Summary

`SqlQuery` lets you execute raw SQL and materialize scalar values or non-entity CLR types without starting from a `DbSet`.

Key points:

* use `SqlQuery<TResult>` for interpolated, parameterized raw SQL that returns scalar values or mappable non-entity types;
* use `SqlQueryRaw<TResult>` when part of the SQL text must be constructed dynamically;
* use terminal operations such as `ToListAsync()` or `SingleAsync()` to execute the query and materialize the results;
* use `SqlQuery` with a CLR type such as a DTO when the result contains multiple columns but does not need to be an entity in the EF Core model;
* compose LINQ over `SqlQuery` when the supplied SQL is composable;
* when EF Core composes over a scalar result and needs to reference the output column, alias that column as `Value`;
* use `FromSql` for mapped entity results and `ExecuteSql` for SQL commands that do not return rows.

## Related Articles

These articles provide the most useful next steps for working with raw SQL, custom result shapes, and query composition in EF Core.

* [FromSql in EF Core](/querying/from-sql) — Query mapped entity types using raw SQL from a `DbSet`.
* [Projection in EF Core](/querying/projection) — Return scalar values, DTOs, and custom result shapes using LINQ instead of writing raw SQL.
* [LINQ Queries in EF Core](/querying/linq-queries) — Learn how EF Core queries are built, composed, and executed.
* **ExecuteSql in EF Core** *(Coming soon)* — Execute SQL commands that do not return rows.
* **Query Parameters in EF Core** *(Coming soon)* — Learn how to pass parameters safely to raw SQL APIs.

## FAQ

These frequently asked questions clarify the main differences and behaviors of `SqlQuery` in EF Core.

### What is the difference between `SqlQuery` and `FromSql`?

Use `SqlQuery` for scalar values or non-entity CLR types. Use `FromSql` for mapped entity types queried from a `DbSet`.

### What is the difference between `SqlQuery` and `SqlQueryRaw`?

`SqlQuery` parameterizes interpolated values automatically. `SqlQueryRaw` accepts a raw SQL string and is useful when part of the SQL text itself must be constructed dynamically.

Values passed separately through `SqlQueryRaw` parameter arguments can still be sent as database parameters.

### Can `SqlQuery` return a DTO?

Yes. `SqlQuery<TResult>` can materialize rows into a mappable CLR type that is not registered as an entity in the EF Core model.

### Can I compose LINQ over `SqlQuery`?

Yes, when the supplied SQL is composable. For scalar queries, alias the output column as `Value` when EF Core needs to reference that column while composing additional SQL over the query.

### Does `SqlQuery` execute immediately?

No. `SqlQuery` returns an `IQueryable<TResult>`. The database query executes when the results are requested through an operation such as `ToListAsync()` or `SingleAsync()`.

