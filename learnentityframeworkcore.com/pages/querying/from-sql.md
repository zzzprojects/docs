---
title: FromSql in EF Core
description: Learn how to use FromSql and FromSqlRaw in EF Core to query entities with raw SQL, pass parameters safely, compose LINQ, include related data, and call stored procedures.
canonical: /querying/from-sql
status: Published
lastmod: 2026-09-04
---

# FromSql in EF Core

`FromSql` lets you start an EF Core query from SQL when you need direct control over the query sent to a relational database.

Use `FromSql` when the SQL query returns entities that are part of your EF Core model.

## Query Entities with `FromSql`

Use `FromSql` directly from a `DbSet<TEntity>` to start a query from SQL.

```csharp
var products = await context.Products
    .FromSql($"SELECT * FROM Products")
    .ToListAsync();
```

`context.Products` identifies the entity type EF Core will materialize. `FromSql(...)` supplies the SQL that forms the starting point of the query, and `ToListAsync()` executes it.

In this example, EF Core executes the SQL and returns a `List<Product>`.

The SQL must return a result that EF Core can materialize as `Product`. The specific column requirements are covered later in [Requirements and Limitations](#requirements-and-limitations).

Calling `FromSql(...)` does not execute the query immediately. It creates an `IQueryable<Product>` that can be further composed when the supplied SQL is composable. The query is executed when a terminal operation such as `ToListAsync()` is called.

`FromSql` must start directly from a `DbSet<TEntity>`. It cannot be added after an arbitrary LINQ query.

For queries that can be expressed clearly with LINQ, you can continue using the regular EF Core querying APIs. See [LINQ Queries in EF Core](/querying/linq-queries).

## Pass Parameters with `FromSql`

Use interpolated values with `FromSql` when the SQL query needs parameters.

```csharp
var minimumPrice = 25m;

var products = await context.Products
    .FromSql($"SELECT * FROM Products WHERE Price >= {minimumPrice}")
    .ToListAsync();
```

Although the SQL is written with C# string interpolation, EF Core does not insert the value of `minimumPrice` directly into the SQL text.

Instead, EF Core converts the interpolated value into a database parameter and sends it separately from the SQL statement.

This means values passed through `FromSql` are parameterized, protecting parameter values from SQL injection.

`FromSqlInterpolated` also supports this interpolated and parameterized pattern. `FromSql`, introduced in EF Core 7, is the primary API used throughout this article.

When the dynamic part of the SQL cannot be represented as a database parameter, such as a column name, `FromSqlRaw` can be used instead.

## Use `FromSqlRaw`

Use `FromSqlRaw` when you need to construct parts of the SQL text dynamically that cannot be passed as database parameters.

For example, database parameters can represent values, but they cannot represent schema elements such as column names.

```csharp
var columnName = "Name";
var columnValue = "Laptop";

var products = await context.Products
    .FromSqlRaw($"SELECT * FROM Products WHERE {columnName} = {{0}}", columnValue)
    .ToListAsync();
```

In this example, `columnName` becomes part of the SQL text because a column name cannot be parameterized.

The value in `columnValue` is different. The `{0}` placeholder and the additional argument passed to `FromSqlRaw` cause EF Core to send that value as a database parameter.

This distinction is important. `FromSqlRaw` is not inherently unsafe, but content inserted directly into the SQL text is not automatically parameterized.

Never insert untrusted user input directly into the SQL string. Any dynamic SQL fragment that becomes part of the SQL text must come from a trusted source or be carefully validated before the query is executed.

Starting with EF Core 10, EF Core includes an analyzer that warns when raw SQL is constructed dynamically in patterns that can introduce unsafe SQL fragments. If the inserted fragment is trusted or has been properly validated, the warning can be suppressed intentionally.

When only values need to vary, prefer `FromSql`, which parameterizes interpolated values automatically.

## Compose LINQ over `FromSql`

When the SQL supplied to `FromSql` is composable, you can continue building the query with LINQ operators.

```csharp
var products = await context.Products
    .FromSql($"SELECT * FROM Products")
    .Where(product => product.IsActive)
    .OrderBy(product => product.ProductId)
    .ToListAsync();
```

`FromSql(...)` provides the starting SQL query. `Where(...)` adds filtering, `OrderBy(...)` adds sorting, and `ToListAsync()` executes the composed query.

EF Core treats the SQL supplied to `FromSql` as a subquery and composes the additional LINQ operations over it.

The supplied SQL must therefore be valid when used as a subquery. Composable SQL generally starts with a `SELECT` statement and cannot contain constructs that are invalid inside a subquery.

Some restrictions are provider-specific. For example, SQL Server does not allow certain query-level constructs, such as a trailing query hint, when the SQL is used as a subquery.

Stored procedure calls are different because they are not composable on SQL Server. We will cover that limitation in the stored procedure section.

## Include Related Data

You can also compose `Include(...)` over `FromSql` to load related data.

```csharp
var products = await context.Products
    .FromSql($"SELECT * FROM Products")
    .Include(product => product.Category)
    .ToListAsync();
```

`FromSql(...)` retrieves the `Product` entities, while `Include(product => product.Category)` tells EF Core to also load the related `Category` navigation.

The raw SQL returns the `Product` entity data, while `Include(...)` adds the related `Category` navigation to the query.

As with other LINQ operators composed over `FromSql`, the supplied SQL must be composable.

For more details about loading related data with `Include` and `ThenInclude`, see [Include in EF Core](/querying/include).

## Call a Stored Procedure with `FromSql`

`FromSql` can also execute a stored procedure that returns data matching an entity type.

The following example uses SQL Server:

```csharp
var categoryId = 1;

var products = await context.Products
    .FromSql($"EXECUTE dbo.GetProductsByCategory {categoryId}")
    .ToListAsync();
```

`FromSql(...)` executes the stored procedure, and EF Core materializes the returned rows as `Product` entities.

The interpolated `categoryId` value is sent as a database parameter rather than being inserted directly into the SQL text.

The stored procedure must return a result that EF Core can materialize as `Product`, following the same requirements as other `FromSql` queries.

Stored procedure syntax and capabilities depend on the database provider. SQLite, for example, does not support stored procedures, so this example is specific to providers such as SQL Server.

### Do Not Compose LINQ over a SQL Server Stored Procedure

SQL Server does not allow EF Core to compose additional SQL over a stored procedure call.

For example, avoid composing a LINQ filter directly over the stored procedure:

```csharp
var products = await context.Products
    .FromSql($"EXECUTE dbo.GetProductsByCategory {categoryId}")
    .Where(product => product.IsActive)
    .ToListAsync();
```

EF Core would try to compose the `Where(...)` operation over the stored procedure call, which results in invalid SQL on SQL Server.

If you intentionally need to continue processing the results on the client, switch from EF Core query composition to client-side enumeration immediately after `FromSql`.

```csharp
var products = context.Products
    .FromSql($"EXECUTE dbo.GetProductsByCategory {categoryId}")
    .AsEnumerable()
    .Where(product => product.IsActive)
    .ToList();
```

Here, the stored procedure is executed first, and the subsequent `Where(...)` operation runs in memory rather than being translated into SQL.

For asynchronous streaming, `AsAsyncEnumerable()` can be used instead.

## Requirements and Limitations

When `FromSql` returns an entity type, the SQL result must provide the data EF Core needs to materialize that entity.

The query must return values for all mapped properties of the entity type, and the column names in the result must match the column names configured in the EF Core model.

For example, if `Product` maps `ProductId`, `Name`, `Price`, `IsActive`, and `CategoryId`, the SQL query must return the columns required for those mapped properties.

```csharp
var products = await context.Products
    .FromSql(
        $"""
        SELECT ProductId, Name, Price, IsActive, CategoryId
        FROM Products
        """)
    .ToListAsync();
```

If a required mapped column is missing from the result, EF Core cannot materialize the `Product` entity correctly and the query fails.

Related entities are not returned as part of the `Product` entity result itself. When the SQL is composable, use `Include(...)` to load related navigations as shown earlier in this article.

`FromSql` is specifically for SQL queries rooted in a `DbSet` that return results for an entity type in the EF Core model.

Use the neighboring raw SQL APIs when the result has a different purpose:

* Use `SqlQuery` when SQL should return scalar values or non-entity result types.
* Use `ExecuteSql` when the SQL command does not return rows, such as an `UPDATE`, `DELETE`, or a stored procedure without a result set.

These APIs are covered separately so that each raw SQL operation uses the API that matches its result.

## Tracking `FromSql` Results

Queries started with `FromSql` or `FromSqlRaw` follow the same change-tracking rules as other EF Core entity queries.

By default, returned `Product` entities are tracked by the current `DbContext`:

```csharp
var products = await context.Products
    .FromSql($"SELECT * FROM Products")
    .ToListAsync();
```

Because the entities are tracked, changes made to them can be detected and saved later through the normal EF Core change-tracking workflow.

Use `AsNoTracking()` when the returned entities do not need to be tracked:

```csharp
var products = await context.Products
    .FromSql($"SELECT * FROM Products")
    .AsNoTracking()
    .ToListAsync();
```

`AsNoTracking()` changes the tracking behavior of the query without changing the fact that `FromSql` returns `Product` entities.

For the available tracking options and their behavior, see [Query Tracking in EF Core](/querying/query-tracking).

## External Resources - FromSql

The following videos complement the examples in this article with practical demonstrations of raw SQL queries in EF Core. Together, they show parameterized `FromSqlRaw` queries, stored procedures executed through `FromSql`, interpolated SQL with automatic parameterization, and the security difference between passing values as parameters and inserting them directly into raw SQL text.

### Video 1 - Raw SQL queries & stored procedures in Entity Framework Core

<iframe width="560" height="315" src="https://www.youtube.com/embed/TMiOAECdodM" title="Raw SQL queries & stored procedures in Entity Framework Core" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

Round The Code demonstrates two raw SQL patterns that are directly relevant to this article. The first starts from a `Products` `DbSet`, uses `FromSqlRaw`, and passes the query value through an explicit `SqlParameter`. The second creates a SQL Server stored procedure and executes it through `FromSql`, returning `Product` entities from the procedure result.

The video uses EF Core 8.0.1 and SQL Server. Although it predates EF Core 10, the selected segments demonstrate the same parameterized `FromSqlRaw` pattern and entity-returning stored procedure usage covered in this article.

**Key timestamps:**

* [2:02](https://www.youtube.com/watch?v=TMiOAECdodM&t=122s) — Using `FromSqlRaw` from the `Products` `DbSet` to start a raw SQL query
* [2:24](https://www.youtube.com/watch?v=TMiOAECdodM&t=144s) — Creating a `SqlParameter` for the query value instead of inserting the value directly into the SQL text
* [6:04](https://www.youtube.com/watch?v=TMiOAECdodM&t=364s) — Using `FromSql` from the `Products` `DbSet` to execute the `GetProductsOrderedByPrice` stored procedure
* [7:38](https://www.youtube.com/watch?v=TMiOAECdodM&t=458s) — Executing the API endpoint and showing the products returned from the stored procedure

### Video 2 - SQL Puro en Entity Framework core Para dejar de llorar porel rendimiento 😭

<iframe width="560" height="315" src="https://www.youtube.com/embed/4oKL6MimvY8" title="SQL Puro en Entity Framework core Para dejar de llorar porel rendimiento 😭" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

NetMentor demonstrates how to execute raw SQL from an EF Core `DbSet` and shows the interpolated SQL API available in the EF Core 7 timeframe.

The most relevant portion uses `FromSqlInterpolated` with an interpolated value such as `userId` and explains that EF Core converts that value into a database parameter instead of inserting it directly into the SQL command text.

Although the video uses the API presentation from an earlier EF Core version, the selected segments demonstrate the same automatic parameterization behavior that remains relevant when interpolated values are passed through EF Core's parameterized raw SQL APIs.

**Key timestamps:**

* [4:42](https://www.youtube.com/watch?v=4oKL6MimvY8&t=282s) — Introducing the raw SQL APIs available in the EF Core version used by the video
* [4:59](https://www.youtube.com/watch?v=4oKL6MimvY8&t=299s) — Introducing `FromSqlInterpolated` for an interpolated raw SQL query
* [5:04](https://www.youtube.com/watch?v=4oKL6MimvY8&t=304s) — Building the query with an interpolated `userId` value
* [5:23](https://www.youtube.com/watch?v=4oKL6MimvY8&t=323s) — Explaining that EF Core parameterizes the interpolated value automatically

### Video 3 - Avoiding SQL Injection in Entity Framework Core (While using inline queries)

<iframe width="560" height="315" src="https://www.youtube.com/embed/rRTCfOMOPMk" title="Avoiding SQL Injection in Entity Framework Core (While using inline queries)" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

DotNet Core Central demonstrates the security difference between passing values as database parameters and inserting them directly into raw SQL text.

The video first shows a raw SQL call where a value is supplied separately and converted by EF Core into a database parameter. It then contrasts a parameterized value with one inserted directly through string interpolation and demonstrates the difference using an attempted SQL injection.

The video uses an earlier generation of EF Core raw SQL APIs. Although the API names differ from current EF Core, the selected segments clearly demonstrate the security principle that remains relevant today: parameterized values are treated as data rather than executable SQL text.

**Key timestamps:**

* [1:36](https://www.youtube.com/watch?v=rRTCfOMOPMk&t=96s) — Passing a stored procedure value separately and explaining that EF Core converts it into a database parameter
* [2:03](https://www.youtube.com/watch?v=rRTCfOMOPMk&t=123s) — Comparing a parameterized value with another value inserted directly through string interpolation
* [7:22](https://www.youtube.com/watch?v=rRTCfOMOPMk&t=442s) — Showing that an attempted SQL injection payload remains data when the query uses parameterization
* [8:05](https://www.youtube.com/watch?v=rRTCfOMOPMk&t=485s) — Reverting to direct interpolation and showing the same payload being interpreted as SQL and truncating the table

## Summary

`FromSql` lets an EF Core query start from SQL and materialize the returned rows as entities that are part of the EF Core model.

* Use `FromSql` for parameterized SQL that returns entities.
* Use `FromSqlRaw` when part of the SQL text must be constructed dynamically.
* Compose LINQ operators and `Include(...)` over `FromSql` only when the supplied SQL is composable.
* `FromSql` and `FromSqlRaw` follow the normal EF Core tracking behavior for entity queries.
* Use `SqlQuery` for scalar or non-entity raw SQL results, and `ExecuteSql` for commands that do not return rows.

## Related Articles

* [Include in EF Core](/querying/include) — Load related entities through navigation properties.
* [Query Tracking in EF Core](/querying/query-tracking) — Understand tracking and no-tracking behavior for queried entities.
* **SqlQuery in EF Core** *(Coming soon)* — Query scalar values and non-entity result types using raw SQL.
* **ExecuteSql in EF Core** *(Coming soon)* — Execute SQL commands that do not return rows.
* **Query Parameters in EF Core** *(Coming soon)* — Learn how to pass parameters safely to raw SQL APIs.

## FAQ

### What is the difference between `FromSql` and `FromSqlRaw`?

`FromSql` parameterizes interpolated values automatically. `FromSqlRaw` is useful when part of the SQL text itself must be constructed dynamically.

### Can I compose LINQ over `FromSql`?

Yes, when the supplied SQL is composable. EF Core treats the SQL as a subquery and can compose operators such as `Where(...)`, `OrderBy(...)`, and `Include(...)` over it.

### Can `FromSql` call a stored procedure?

Yes, when the database provider supports stored procedures and the procedure returns data that EF Core can materialize as the target entity type.

On SQL Server, do not compose additional LINQ operators directly over the stored procedure call.

### Are entities returned by `FromSql` tracked?

By default, yes. Use `AsNoTracking()` when the returned entities do not need to be tracked by the current `DbContext`.

### Can `FromSql` return only some columns from an entity?

Not when EF Core needs to materialize the mapped entity. The SQL result must provide the required mapped columns. Use `SqlQuery` when the result should instead be scalar or non-entity data.
