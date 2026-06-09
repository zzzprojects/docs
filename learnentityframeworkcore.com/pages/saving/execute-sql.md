---
title: EF Core ExecuteSql
description: Learn how to use ExecuteSql in EF Core to run raw SQL commands such as INSERT, UPDATE, and DELETE directly in the database.
canonical: /saving/execute-sql
status: Published
lastmod: 2026-06-09
---

# EF Core ExecuteSql

`ExecuteSqlAsync()` executes raw SQL commands directly in the database and returns the number of rows affected.

It is useful when you want to run SQL that does not return data, such as an `INSERT`, `UPDATE`, `DELETE`, or stored procedure that modifies data.

## Insert a Row with ExecuteSql

Use `ExecuteSqlAsync()` to execute an `INSERT` statement directly in the database.

```csharp
using var context = new AppDbContext();

var rowsAffected = await context.Database.ExecuteSqlAsync($"""
    INSERT INTO [Blogs] ([Name], [Rating])
    VALUES ({"ZZZ Projects"}, {5})
""");
```

This inserts one row into the `Blogs` table.

The command is sent directly to the database.

`rowsAffected` contains the number of rows inserted.

No `Blog` instance is created or tracked by the current `DbContext`.

## Update Rows with ExecuteSql

Use `ExecuteSqlAsync()` to execute an `UPDATE` statement directly in the database.

```csharp
using var context = new AppDbContext();

var rowsAffected = await context.Database.ExecuteSqlAsync($"""
    UPDATE [Blogs]
    SET [Rating] = {5}
    WHERE [Name] = {"ZZZ Projects"}
""");
```

This updates all rows in the `Blogs` table where `Name` is `ZZZ Projects`.

`rowsAffected` contains the number of rows updated.

If the `WHERE` condition matches more than one row, all matching rows are updated.

## Delete Rows with ExecuteSql

Use `ExecuteSqlAsync()` to execute a `DELETE` statement directly in the database.

```csharp
using var context = new AppDbContext();

var rowsAffected = await context.Database.ExecuteSqlAsync($"""
    DELETE FROM [Blogs]
    WHERE [Rating] < {3}
""");
```

This deletes all rows in the `Blogs` table where `Rating` is lower than `3`.

`rowsAffected` contains the number of rows deleted.

Be careful with the `WHERE` clause. If it is missing or too broad, more rows can be deleted than expected.

## Use Parameters Safely

Prefer `ExecuteSqlAsync()` with interpolated values when passing application values into a SQL command.

```csharp
using var context = new AppDbContext();

var name = "ZZZ Projects";
var rating = 5;

await context.Database.ExecuteSqlAsync($"""
    UPDATE [Blogs]
    SET [Rating] = {rating}
    WHERE [Name] = {name}
""");
```

EF Core converts the interpolated values into database parameters.

Do not build SQL commands by concatenating user input into the SQL string.


## ExecuteSql vs ExecuteSqlRaw

Use `ExecuteSqlAsync()` when values can be passed as parameters.

Use `ExecuteSqlRawAsync()` only when part of the SQL text itself must be dynamic, such as a column name, table name, or provider-specific SQL fragment.

Values should still be passed as parameters whenever possible.

If you use `ExecuteSqlRawAsync()`, keep values parameterized explicitly instead of concatenating them into the SQL string.

Be careful with dynamic SQL. Any value inserted directly into the SQL string must come from a trusted source or be validated first.

## Use Insert, Update, and Delete Together

The following example shows how `INSERT`, `UPDATE`, and `DELETE` commands can be executed from the same `DbContext`.

```csharp
using var context = new AppDbContext();

await context.Database.ExecuteSqlAsync($"""
    INSERT INTO [Blogs] ([Name], [Rating])
    VALUES ({"Temporary Blog"}, {1})
""");

await context.Database.ExecuteSqlAsync($"""
    UPDATE [Blogs]
    SET [Rating] = {2}
    WHERE [Name] = {"Temporary Blog"}
""");

await context.Database.ExecuteSqlAsync($"""
    DELETE FROM [Blogs]
    WHERE [Name] = {"Temporary Blog"}
""");
```

Each command is sent to the database when it is called.

These commands do not wait for `SaveChangesAsync()`.

If one command succeeds and a later command fails, the previous command has already been executed unless you use a transaction.

## Use ExecuteSql with a Transaction

Use a transaction when several raw SQL commands must succeed or fail together.

```csharp
using var context = new AppDbContext();

await using var transaction = await context.Database.BeginTransactionAsync();

try
{
    await context.Database.ExecuteSqlAsync($"""
        INSERT INTO [Blogs] ([Name], [Rating])
        VALUES ({"Temporary Blog"}, {1})
    """);

    await context.Database.ExecuteSqlAsync($"""
        UPDATE [Blogs]
        SET [Rating] = {2}
        WHERE [Name] = {"Temporary Blog"}
    """);

    await context.Database.ExecuteSqlAsync($"""
        DELETE FROM [Blogs]
        WHERE [Name] = {"Temporary Blog"}
    """);

    await transaction.CommitAsync();
}
catch
{
    await transaction.RollbackAsync();
    throw;
}
```

The transaction ensures that the three commands are treated as one unit of work.

If one command fails before `CommitAsync()`, the transaction can be rolled back and the previous commands are not permanently saved.

For more details, see the Microsoft documentation on [EF Core transactions](https://learn.microsoft.com/en-us/ef/core/saving/transactions).

## Important Behavior

`ExecuteSqlAsync()` executes immediately.

That means:

* it does not wait for `SaveChangesAsync()`
* it does not use the `ChangeTracker`
* it does not load entities into memory
* it sends the SQL command directly to the database
* it returns the number of rows affected
* database constraints, foreign keys, triggers, and transaction rules still apply

For tracked entity workflows, see [SaveChanges](/saving/save-changes).

For normal tracked inserts, updates, and deletes, see [Adding Data](/saving/adding-data), [Updating Data](/saving/modifying-data), and [Deleting Data](/saving/deleting-data).

## ExecuteSql Requirements and Limitations

`ExecuteSqlAsync()` is designed for SQL commands that do not return data.

Use it for commands such as:

* `INSERT`
* `UPDATE`
* `DELETE`
* stored procedures that modify data and do not return result sets

The SQL command must be valid for your database provider. For example, SQL Server, PostgreSQL, MySQL, and SQLite can use different SQL syntax for some operations.

`ExecuteSqlAsync()` is not used to query entities. To query entities with raw SQL, use APIs such as `FromSql`.

For more details, see the Microsoft documentation on [executing non-querying SQL](https://learn.microsoft.com/en-us/ef/core/querying/sql-queries?tabs=sqlserver#executing-non-querying-sql).

## How ExecuteSql Works

`ExecuteSqlAsync()` does not follow the normal tracked entity workflow.

When you call `SaveChangesAsync()`, EF Core looks at tracked entities, detects their states, and generates the required SQL commands.

`ExecuteSqlAsync()` works differently. The SQL command is already written by you.

```csharp
using var context = new AppDbContext();

await context.Database.ExecuteSqlAsync($"""
    UPDATE [Blogs]
    SET [Rating] = {5}
    WHERE [Name] = {"ZZZ Projects"}
""");
```

EF Core uses the database connection, parameterizes interpolated values, sends the command to the database, and returns the number of rows affected.

No entity instance is loaded, and the `ChangeTracker` is not involved.

## ExecuteSql vs SaveChanges

`ExecuteSqlAsync()` and `SaveChangesAsync()` can both persist data, but they are designed for different workflows.

| Feature                             | `ExecuteSqlAsync()` | `SaveChangesAsync()`     |
| ----------------------------------- | ------------------- | ------------------------ |
| SQL command                         | Written by you      | Generated by EF Core     |
| Uses tracked entities               | No                  | Yes                      |
| Uses ChangeTracker                  | No                  | Yes                      |
| Requires `SaveChangesAsync()`       | No                  | It is the save operation |
| Loads entities                      | No                  | Usually yes              |
| Updates tracked instances in memory | No                  | Yes                      |
| Best for                            | Raw SQL commands    | Normal entity workflows  |

Use `SaveChangesAsync()` when you are working with entities and want EF Core to generate the SQL for you.

Use `ExecuteSqlAsync()` when you need to write and run the SQL command yourself.

## ExecuteSql vs ExecuteUpdate and ExecuteDelete

`ExecuteSqlAsync()`, `ExecuteUpdateAsync()`, and `ExecuteDeleteAsync()` all execute directly in the database, but they are used for different scenarios.

| Scenario                                   | Recommended API        |
| ------------------------------------------ | ---------------------- |
| Insert, update, or delete tracked entities | `SaveChangesAsync()`   |
| Update rows with a LINQ query              | `ExecuteUpdateAsync()` |
| Delete rows with a LINQ query              | `ExecuteDeleteAsync()` |
| Run raw SQL that modifies data             | `ExecuteSqlAsync()`    |

Use `ExecuteUpdateAsync()` or `ExecuteDeleteAsync()` when the operation can be expressed with a LINQ query.

Use `ExecuteSqlAsync()` when you need to write the SQL command yourself.

`ExecuteSqlAsync()` gives you more control, but you are responsible for writing valid SQL for your database provider.

## When to Use ExecuteSql

Use `ExecuteSqlAsync()` when:

* you need to run a raw `INSERT`, `UPDATE`, or `DELETE`
* you need to execute a stored procedure that modifies data and does not return a result set
* the operation cannot be expressed clearly with LINQ
* you need provider-specific SQL
* you want to know how many rows were affected
* you understand and control the SQL command being executed

## When Not to Use ExecuteSql

Avoid `ExecuteSqlAsync()` when:

* the normal tracked entity workflow is clearer
* `ExecuteUpdateAsync()` or `ExecuteDeleteAsync()` can express the operation safely with LINQ
* the SQL depends on unsafe user input
* you need tracked entities to stay synchronized automatically
* you want code that is easier to move between database providers
* you do not fully control or understand the SQL command

For example, if your application loads an entity, applies business rules, validates changes, and then saves it, a tracked workflow is usually clearer.

```csharp
using var context = new AppDbContext();

var blog = await context.Blogs
    .FirstAsync(blog => blog.Name == "ZZZ Projects");

blog.Rating = 5;

await context.SaveChangesAsync();
```

For normal tracked updates, see [Updating Data](/saving/modifying-data).

## Common Pitfalls

Be careful with the following mistakes.

### Expecting SaveChangesAsync to Be Required

`ExecuteSqlAsync()` runs immediately.

This is unnecessary:

```csharp
using var context = new AppDbContext();

await context.Database.ExecuteSqlAsync($"""
    UPDATE [Blogs]
    SET [Rating] = {5}
    WHERE [Name] = {"ZZZ Projects"}
""");

await context.SaveChangesAsync();
```

The `SaveChangesAsync()` call does not save the `ExecuteSqlAsync()` operation. The SQL command already ran.

### Forgetting the WHERE Clause

A raw SQL `UPDATE` or `DELETE` can affect more rows than expected if the `WHERE` clause is missing or too broad.

```csharp
using var context = new AppDbContext();

await context.Database.ExecuteSqlAsync($"""
    DELETE FROM [Blogs]
""");
```

This deletes all rows from the `Blogs` table.

Only run this kind of command when deleting every row is intentional.

### Building SQL with String Concatenation

Avoid building SQL commands by concatenating user input into the SQL string.

```csharp
var name = "ZZZ Projects";

await context.Database.ExecuteSqlRawAsync(
    "DELETE FROM [Blogs] WHERE [Name] = '" + name + "'");
```

This pattern is harder to validate and can expose your application to SQL injection if user input is involved.

Prefer `ExecuteSqlAsync()` with interpolated values when values can be passed as parameters.

### Expecting Tracked Entities to Be Updated in Memory

`ExecuteSqlAsync()` does not update tracked entity instances already loaded in the current `DbContext`.

```csharp
using var context = new AppDbContext();

var blog = await context.Blogs
    .FirstAsync(blog => blog.Name == "ZZZ Projects");

await context.Database.ExecuteSqlAsync($"""
    UPDATE [Blogs]
    SET [Rating] = {5}
    WHERE [Name] = {"ZZZ Projects"}
""");

// The tracked blog instance may still have the old Rating value.
```

If you need fresh values, reload the entity or use a new `DbContext`.

## External Resources - ExecuteSql

The following videos are useful if you want to see how raw SQL commands are executed from EF Core, especially how `ExecuteSqlRaw` differs from query-based APIs such as `FromSqlRaw`.

### Video 1 - Entity Framework Core (Inline Query for Insert, Update and Delete)

<iframe width="560" height="315" src="https://www.youtube.com/embed/oEEcLVddmBs" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

This video shows how to run raw SQL commands for `INSERT`, `UPDATE`, and `DELETE` operations in Entity Framework Core using `ExecuteSqlRaw`. It is useful as a practical example of executing non-query SQL commands from EF Core.

The article uses `ExecuteSqlAsync()` for safer interpolated values, while this video focuses on `ExecuteSqlRaw` with explicit parameters. When adapting the pattern, keep values parameterized and avoid string concatenation.

Key timestamps:

* [2:00](https://www.youtube.com/watch?v=oEEcLVddmBs&t=120) — Safe parameters with `SqlParameter`
* [3:30](https://www.youtube.com/watch?v=oEEcLVddmBs&t=210) — Updating records with `ExecuteSqlRaw`
* [7:15](https://www.youtube.com/watch?v=oEEcLVddmBs&t=435) — Deleting records with `ExecuteSqlRaw`
* [10:00](https://www.youtube.com/watch?v=oEEcLVddmBs&t=600) — Inserting records with `ExecuteSqlRaw`

### Video 2 - FromSqlRaw vs ExecuteSqlRaw in ASP.NET Core

<iframe width="560" height="315" src="https://www.youtube.com/embed/HHaCTg0kTbA" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

This video explains the difference between `FromSqlRaw` and `ExecuteSqlRaw` in ASP.NET Core. It is useful for understanding why raw SQL APIs that return entities are different from raw SQL commands used to modify data.

The most relevant part for this article is the discussion of `ExecuteSqlRaw`, since `FromSqlRaw` is mainly used for queries that return entity data.

Key timestamps:

* [0:00](https://www.youtube.com/watch?v=HHaCTg0kTbA&t=0) — Introduction to `FromSqlRaw` and `ExecuteSqlRaw`
* [4:00](https://www.youtube.com/watch?v=HHaCTg0kTbA&t=240) — Difference between raw SQL queries and raw SQL commands
* [5:00](https://www.youtube.com/watch?v=HHaCTg0kTbA&t=300) — Explanation of `ExecuteSqlRaw`
* [5:30](https://www.youtube.com/watch?v=HHaCTg0kTbA&t=330) — Example of using `ExecuteSqlRaw` to modify data

## Summary

`ExecuteSqlAsync()` lets you execute raw SQL commands directly in the database.

Key points:

* it can run commands such as `INSERT`, `UPDATE`, and `DELETE`
* it can execute stored procedures that modify data without returning result sets
* it executes immediately
* it does not use the `ChangeTracker`
* it does not require `SaveChangesAsync()`
* it returns the number of rows affected
* interpolated values are parameterized
* transactions are useful when several commands must succeed or fail together

Use `SaveChangesAsync()` when you want EF Core to generate SQL from tracked entity changes.

Use `ExecuteUpdateAsync()` or `ExecuteDeleteAsync()` when a LINQ-based direct database operation is enough.

Use `ExecuteSqlAsync()` when you need to write and execute the SQL command yourself.

## Related Articles

If you want to compare `ExecuteSqlAsync()` with the most closely related EF Core saving patterns, these pages are the best next step:

* [SaveChanges](/saving/save-changes) — how EF Core persists tracked changes
* [Adding Data](/saving/adding-data) — how to insert entities with the normal tracked workflow
* [Updating Data](/saving/modifying-data) — how to update entities with the normal tracked workflow
* [Deleting Data](/saving/deleting-data) — how to delete entities with the normal tracked workflow
* [ExecuteUpdate](/saving/execute-update) — how to update rows directly in the database using a LINQ query
* [ExecuteDelete](/saving/execute-delete) — how to delete rows directly in the database using a LINQ query

## FAQ

### Does ExecuteSql require SaveChangesAsync?

No. `ExecuteSqlAsync()` executes immediately in the database. You do not need to call `SaveChangesAsync()` afterward.

### Does ExecuteSql use the ChangeTracker?

No. `ExecuteSqlAsync()` does not use the `ChangeTracker`. It sends the SQL command directly to the database.

### What does ExecuteSql return?

`ExecuteSqlAsync()` returns the number of rows affected by the SQL command.

### Can ExecuteSql run INSERT, UPDATE, and DELETE commands?

Yes. You can use `ExecuteSqlAsync()` to run raw SQL commands such as `INSERT`, `UPDATE`, and `DELETE`.

Be careful with `UPDATE` and `DELETE` commands. If the `WHERE` clause is missing or too broad, more rows can be affected than expected.

### Is ExecuteSql safe from SQL injection?

`ExecuteSqlAsync()` parameterizes interpolated values.

Avoid building SQL commands by concatenating user input into the SQL string.

### When should I use ExecuteSql instead of ExecuteUpdate or ExecuteDelete?

Use `ExecuteUpdateAsync()` or `ExecuteDeleteAsync()` when the operation can be expressed with a LINQ query.

Use `ExecuteSqlAsync()` when you need to write and execute the SQL command yourself.