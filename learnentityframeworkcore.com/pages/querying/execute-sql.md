---
title: ExecuteSql in EF Core
description: Learn how to use ExecuteSql and ExecuteSqlRaw in EF Core to execute SQL commands that do not return result rows, including stored procedures.
canonical: /querying/execute-sql
status: Published
lastmod: 2026-09-11
---

# ExecuteSql in EF Core

`ExecuteSql` lets you execute SQL commands directly against the database when the command does not return result rows. This is useful for non-query SQL operations, including data-modification commands and stored procedures that do not return result sets.

If you are comparing raw SQL commands with `SaveChanges`, `ExecuteUpdate`, or `ExecuteDelete`, see [ExecuteSql in the Saving section](/saving/execute-sql).

For raw SQL that returns mapped entity results, use [FromSql](/querying/from-sql). For scalar or non-entity results, use [SqlQuery](/querying/sql-query).

## Use `ExecuteSql`

Use `ExecuteSqlAsync()` to execute an interpolated SQL command asynchronously.

```csharp
var productId = 1;
var newName = "Gaming Laptop";

var rowsAffected = await context.Database.ExecuteSqlAsync($"""
    UPDATE Products
    SET Name = {newName}
    WHERE ProductId = {productId}
""");
```

This command updates the matching product directly in the database.

`ExecuteSqlAsync()` executes the SQL immediately and returns an `int` representing the result of the non-query command. For this `UPDATE`, the value represents the number of rows affected.

The application receives the command result as an `int`; it does not materialize `Product` entities or another result set.

## Pass Parameters with `ExecuteSql`

Values interpolated into `ExecuteSql` are converted into database parameters rather than being inserted directly into the SQL text.

In the previous example, both `newName` and `productId` are sent as parameters.

This makes `ExecuteSql` the preferred option when the variable parts of the command are data values such as identifiers, names, dates, or numeric values.

When part of the SQL syntax itself must be constructed dynamically, use `ExecuteSqlRaw` instead.

For a broader explanation of parameterization across EF Core raw SQL APIs, see **Query Parameters in EF Core** *(Coming soon)*.

## Use `ExecuteSqlRaw`

`ExecuteSql` works well when the variable parts of the command are data values. When part of the SQL text itself must be constructed dynamically, use `ExecuteSqlRawAsync()` instead.

Database parameters can represent values, but they cannot represent SQL identifiers such as column names.

```csharp
var columnName = "Name";
var newValue = "Gaming Laptop";
var productId = 1;

var sql =
    $"UPDATE Products SET {columnName} = {{0}} WHERE ProductId = {{1}}";

var rowsAffected = await context.Database.ExecuteSqlRawAsync(
    sql,
    newValue,
    productId);
```

In this example, `columnName` becomes part of the SQL text because a column name cannot be passed as a database parameter.

`newValue` and `productId` are handled differently. The `{0}` and `{1}` placeholders correspond to the additional arguments passed to `ExecuteSqlRawAsync()`, so those values are sent as database parameters.

For this `UPDATE`, `rowsAffected` represents the number of rows affected by the command.

Use `ExecuteSqlRawAsync()` when the SQL structure itself must vary. Content inserted directly into the SQL text should come from a trusted source or be carefully validated.

When only data values need to vary, prefer `ExecuteSql`.

`ExecuteSqlRawAsync()` can also receive explicit `DbParameter` instances when you need more control over parameter configuration. For a broader explanation of parameterization and `DbParameter`, see **Query Parameters in EF Core** *(Coming soon)*.

## Execute a Stored Procedure

The same non-query API can also execute a stored procedure when the procedure performs an operation without returning a result set.

For example, suppose a SQL Server database contains a stored procedure named `UpdateProductPrice`:

```csharp
var productId = 1;
var newPrice = 1499.99m;

var result = await context.Database.ExecuteSqlAsync($"""
    EXEC UpdateProductPrice
        @ProductId = {productId},
        @NewPrice = {newPrice}
""");
```

The interpolated values are sent as database parameters, just as they are with other `ExecuteSql` commands.

This example is SQL Server-specific because stored procedure syntax and support depend on the database provider.

`ExecuteSqlAsync()` executes the procedure as a non-query command and does not materialize a result set.

### Stored Procedures and the Return Value

Do not assume that the value returned by `ExecuteSqlAsync()` always represents the number of rows modified inside a stored procedure.

For straightforward `INSERT`, `UPDATE`, and `DELETE` commands, the returned value normally represents the number of rows affected. With stored procedures, however, the value can depend on the database provider and on how the procedure is implemented.

For example, on SQL Server, a stored procedure that uses `SET NOCOUNT ON` can still modify rows successfully while `ExecuteSqlAsync()` returns `-1` instead of the number of rows affected.

Triggers and other statements can also affect the value reported by the underlying non-query command.

If the application needs values returned by the stored procedure rather than simply executing a non-query operation, use the EF Core API that matches the required result shape instead of `ExecuteSql`.

## Important `ExecuteSql` Behavior

The examples above show how to execute non-query SQL commands. A few additional behaviors are important when using these APIs in real applications.

### `ExecuteSql` Executes Immediately

`ExecuteSqlAsync()` sends the command to the database when the method is called.

It does not add a pending entity change to the `ChangeTracker`, and the command does not wait for a later call to `SaveChangesAsync()`.

```csharp
var productId = 1;
var newName = "Gaming Laptop";

await context.Database.ExecuteSqlAsync($"""
    UPDATE Products
    SET Name = {newName}
    WHERE ProductId = {productId}
""");
```

Once the call completes successfully, the SQL command has already been executed against the database.

If your goal is to compare this behavior with tracked entity changes and `SaveChangesAsync()`, see [ExecuteSql in the Saving section](/saving/execute-sql).

### Understand the Return Value

The synchronous `ExecuteSql` APIs return an `int`, and the asynchronous APIs return a `Task<int>`.

For straightforward data-modification commands, the returned value normally represents the number of rows affected by the command.

However, the exact value can depend on the database command and provider behavior. Stored procedures are an important example, as shown above.

For that reason, treat the returned `int` as the result reported by the database for the executed non-query command rather than assuming that every command reports affected rows in exactly the same way.

### Transactions Are Not Started Automatically

`ExecuteSql`, `ExecuteSqlRaw`, and their asynchronous variants do not automatically start a transaction for the command.

When several commands must succeed or fail as one unit, start a transaction explicitly.

```csharp
using var transaction = await context.Database.BeginTransactionAsync();

try
{
    await context.Database.ExecuteSqlAsync($"""
        UPDATE Products
        SET Price = {999.99m}
        WHERE ProductId = {1}
    """);

    await context.Database.ExecuteSqlAsync($"""
        UPDATE Products
        SET Price = {1499.99m}
        WHERE ProductId = {2}
    """);

    await transaction.CommitAsync();
}
catch
{
    await transaction.RollbackAsync();
    throw;
}
```

If the transaction is rolled back, the `ExecuteSqlAsync()` changes made inside that transaction are rolled back with it.

For a broader explanation of transaction workflows, see [Transactions in EF Core](/saving/transactions).

### Execution Strategies Are Not Applied Automatically

`ExecuteSql` and `ExecuteSqlRaw` do not automatically use the current EF Core execution strategy.

This matters when a provider is configured with retry behavior for transient database failures.

Raw SQL commands may not be safe to repeat automatically, especially when an operation is not idempotent.

If retry behavior is required, the execution strategy can be invoked explicitly. When the operation is not idempotent, retry behavior should be coordinated carefully with transaction handling so that a retry does not leave the database in an unexpected state.

This is an advanced reliability concern rather than a requirement for every `ExecuteSql` call.

## Related API Variants

The main examples in this article use asynchronous APIs because they fit modern EF Core application code, but synchronous forms are also available.

For example:

```csharp
var rowsAffected = context.Database.ExecuteSql($"""
    UPDATE Products
    SET Name = {"Gaming Laptop"}
    WHERE ProductId = {1}
""");
```

`ExecuteSqlRaw()` is the synchronous counterpart to `ExecuteSqlRawAsync()`.

EF Core also exposes `ExecuteSqlInterpolated()` and `ExecuteSqlInterpolatedAsync()`. These APIs accept interpolated SQL through a `FormattableString` and parameterize interpolated values.

They remain available, but this article uses `ExecuteSql` and `ExecuteSqlAsync` for the interpolated SQL pattern.

Older APIs such as `ExecuteSqlCommand` are obsolete and should not be used in new EF Core code.

## External Resources

The following videos provide additional examples and explanations related to executing non-query SQL commands and stored procedures with EF Core.

### EF Core + Stored Procedures: The Right Way to Call Legacy SQL

<iframe width="560" height="315" src="https://www.youtube.com/embed/hXfHWvezBoA" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

This video shows how to execute database functions and stored procedures with modern EF Core.

The most relevant section for this article demonstrates a stored procedure that modifies data without returning a result set. It uses `ExecuteSqlAsync()`, passes values through an interpolated `FormattableString`, and shows that those values are converted into database parameters.

The example uses PostgreSQL, so the stored procedure syntax and return-value behavior are provider-specific. However, the `ExecuteSqlAsync()` and parameterization concepts are directly relevant to EF Core.

Key timestamps:

* [7:27](https://www.youtube.com/watch?v=hXfHWvezBoA&t=447) — Stored procedure that performs database logic and updates data
* [8:45](https://www.youtube.com/watch?v=hXfHWvezBoA&t=525) — Using `ExecuteSqlAsync()` for a procedure that does not return a result
* [9:03](https://www.youtube.com/watch?v=hXfHWvezBoA&t=543) — Interpolated values are converted into SQL parameters
* [9:11](https://www.youtube.com/watch?v=hXfHWvezBoA&t=551) — The command returns `-1` while the stored procedure still updates the data

### FromSqlRaw vs ExecuteSqlRaw in ASP NET Core

<iframe width="560" height="315" src="https://www.youtube.com/embed/HHaCTg0kTbA" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

This video explains the difference between `FromSqlRaw` and `ExecuteSqlRaw` and when to use each one.

It is especially useful for understanding the boundary between raw SQL that returns entities and raw SQL commands that perform database operations without returning entities. The video also shows parameter passing and a stored procedure executed with `ExecuteSqlRaw`.

The video predates the `ExecuteSql` / `ExecuteSqlAsync` APIs and focuses on `ExecuteSqlRaw`. `ExecuteSqlRaw` remains available in current EF Core, and the distinction between query APIs and non-query command APIs remains directly relevant.

Key timestamps:

* [0:11](https://www.youtube.com/watch?v=HHaCTg0kTbA&t=11) — `FromSqlRaw` for SQL queries or stored procedures that return entities
* [2:49](https://www.youtube.com/watch?v=HHaCTg0kTbA&t=169) — Passing parameters with placeholders or a `SqlParameter`
* [3:14](https://www.youtube.com/watch?v=HHaCTg0kTbA&t=194) — `ExecuteSqlRaw` for database operations that do not return entities
* [4:43](https://www.youtube.com/watch?v=HHaCTg0kTbA&t=283) — Executing an insert stored procedure with `ExecuteSqlRaw` and returning an integer result

### Execute Stored Procedures in EF Core | Complete Tutorial

<iframe width="560" height="315" src="https://www.youtube.com/embed/UiCDtrhOnGg" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

This video demonstrates several ways to call stored procedures from EF Core, including a scenario where a procedure performs a database operation without returning a result set.

The most relevant section for this article shows a stored procedure that inserts a new department and is executed through the database command API rather than through a query that materializes results.

The video also contrasts stored procedures that return data with stored procedures that only perform a database operation, which helps reinforce the distinction between query APIs and non-query command APIs.

Key timestamps:

* [8:15](https://www.youtube.com/watch?v=UiCDtrhOnGg&t=495) — Introduces a stored procedure that inserts a record without returning data
* [9:00](https://www.youtube.com/watch?v=UiCDtrhOnGg&t=540) — Explains that no result class is needed when the stored procedure does not return output
* [9:14](https://www.youtube.com/watch?v=UiCDtrhOnGg&t=554) — Shows the non-query raw SQL command used to execute the stored procedure
* [10:54](https://www.youtube.com/watch?v=UiCDtrhOnGg&t=654) — Verifies in the database that the new department was inserted successfully

## Summary

`ExecuteSql` lets you execute SQL commands that do not return result rows.

Key points:

* use `ExecuteSqlAsync()` when variable data values can be passed through interpolation and parameterized by EF Core;
* use `ExecuteSqlRawAsync()` when part of the SQL structure itself must be constructed dynamically;
* keep data values parameterized even when using `ExecuteSqlRawAsync()`;
* use `ExecuteSql` for stored procedures that perform non-query operations without returning a result set;
* the returned `int` reports the result of the non-query command, although its exact meaning can depend on the command and database provider;
* `ExecuteSql` commands execute immediately and do not wait for `SaveChangesAsync()`;
* `ExecuteSql` does not automatically start a transaction;
* the current EF Core execution strategy is not automatically used for these commands;
* synchronous variants and the `ExecuteSqlInterpolated` API family are also available.

Use [FromSql](/querying/from-sql) when raw SQL should return mapped entities, and [SqlQuery](/querying/sql-query) when it should return scalar or non-entity results.

## Related Articles

The following articles cover the closest raw SQL and saving scenarios:

* [FromSql in EF Core](/querying/from-sql) — Query mapped entity types using raw SQL.
* [SqlQuery in EF Core](/querying/sql-query) — Query scalar values and non-entity CLR types using raw SQL.
* **Query Parameters in EF Core** *(Coming soon)* — Learn how parameters are handled across EF Core raw SQL APIs.
* [ExecuteSql in the Saving section](/saving/execute-sql) — Compare raw SQL commands with `SaveChanges`, `ExecuteUpdate`, `ExecuteDelete`, and tracked saving workflows.
* [Transactions in EF Core](/saving/transactions) — Group multiple database operations into a transaction when they must succeed or fail together.

## FAQ

### What is the difference between `ExecuteSql` and `ExecuteSqlRaw`?

`ExecuteSql` parameterizes interpolated values automatically.

Use `ExecuteSqlRaw` when part of the SQL text itself must be constructed dynamically.

### Does `ExecuteSqlAsync()` require `SaveChangesAsync()`?

No. `ExecuteSqlAsync()` executes the command when it is called.

### Can `ExecuteSql` execute a stored procedure?

Yes, when the stored procedure is executed as a non-query operation and does not need EF Core to materialize a result set.

### What does `ExecuteSql` return?

`ExecuteSql` returns an `int`, while `ExecuteSqlAsync()` returns a `Task<int>`.

For straightforward data-modification commands, the value normally represents the number of rows affected.

### Does `ExecuteSql` use the `ChangeTracker`?

No. `ExecuteSql` executes the SQL command directly and does not create tracked entity changes that need to be saved later.

### What is the difference between `ExecuteSql`, `FromSql`, and `SqlQuery`?

Use `ExecuteSql` for SQL commands that do not return result rows.

Use [FromSql](/querying/from-sql) for raw SQL that returns mapped entities, and [SqlQuery](/querying/sql-query) for scalar or non-entity results.
