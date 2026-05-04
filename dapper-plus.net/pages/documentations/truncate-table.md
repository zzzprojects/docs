---
Title: Truncate Table with Dapper Plus: Fast & Easy Data Reset in C#
MetaDescription: Learn how to truncate tables with Dapper Plus in C#. Fast, simple, and flexible with SQL Server & PostgreSQL options. Save time and keep code clean.
LastMod: 2026-05-04
---

# Truncate Table

The `TruncateTable` method is a free utility method that allows you to truncate data from a table.

A truncate table statement quickly erases all rows from a database table. Depending on the options, the behavior might be different.

## Truncate a Mapped Table

Truncate a table that is [mapped](/mapping) with Dapper Plus.

```csharp
// @nuget: Z.Dapper.Plus
using Z.Dapper.Plus;

DapperPlusManager.Entity<EntitySimple>().Table("MyTableName");

// ...code...
connection.TruncateTable<EntitySimple>();
```

## Truncate a Mapped Table with a Mapping Key

Truncate a table that uses a [mapping key](/mapping-key).

```csharp
// @nuget: Z.Dapper.Plus
using Z.Dapper.Plus;

DapperPlusManager.Entity<EntitySimple>("MyMappingKey").Table("MyTableName");

// ...code...
connection.TruncateTable<EntitySimple>("MyMappingKey");
```

## Truncate a Table by Name

Truncate a table by explicitly specifying the table name (or schema + table name).

```csharp
// @nuget: Z.Dapper.Plus
using Z.Dapper.Plus;

connection.TruncateTable("MyTableName");
connection.TruncateTable<EntitySimple>("dbo.MyTableName");
```

## Truncate a Table with Options

Truncate a table by specifying some options related to the database provider.

```csharp
// @nuget: Z.Dapper.Plus
using Z.Dapper.Plus;

// SQL Server
connection.TruncateTable<EntitySimple>(
    new SqlServerTruncateTableOptions() 
    { 
        PartitionNumbers = new List<int> { 1, 2, 3 } 
    });

// PostgreSQL
connection.TruncateTable<EntitySimple>(
    new PostgreSqlTruncateTableOptions() 
    { 
        UseRestartIdentity = false 
    });
```

## Options

### SqlServerTruncateTableOptions

* **PartitionNumbers:** Specify an explicit list of partition numbers to use with the `TRUNCATE` statement. Only those partitions will be truncated. (See [With Partitions](https://learn.microsoft.com/th-th/sql/t-sql/statements/truncate-table-transact-sql?view=sql-server-ver17#with--partitions---partition_number_expression--range----n---))

### PostgreSqlTruncateTableOptions

To learn more about PostgreSQL truncate options, see the [official documentation](https://www.postgresql.org/docs/current/sql-truncate.html).

* **TableNames:** Specify one or multiple table names to truncate. When using this option, you should not specify the table name in any other way.
* **UseOnly:** Gets or sets whether `ONLY` should be used to generate the truncate statement.
* **UseRestartIdentity:** Gets or sets whether `RESTART IDENTITY` should be used to generate the truncate statement.
* **UseCascade:** Gets or sets whether `CASCADE` should be used to generate the truncate statement.
* **UseRestrict:** Gets or sets whether `RESTRICT` should be used to generate the truncate statement.

## Compatibility

**Database Providers:**

* SQL Server
* PostgreSQL

## Summary

Dapper Plus makes it even easier to truncate your tables. You no longer have to write or maintain SQL statements yourself; you simply call the method and let the library handle everything for you.

You save time, reduce errors, and keep your code clean and consistent across your entire project.