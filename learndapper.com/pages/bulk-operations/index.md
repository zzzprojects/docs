---
title: Dapper Bulk Operations
description: The Dapper Plus library allows developers to easily perform bulk operations. It helps to optimize performance by reducing the number of database round trips.
canonical: /bulk-operations
status: Published
lastmod: 2025-08-19
---

# Bulk Operations With Dapper

Dapper does not provide a formal API for performing bulk inserts or other bulk operations. However, those operations are possible using a third-party library called [Dapper Plus](https://dapper-plus.net/).

[Dapper Plus](https://dapper-plus.net/) library is a comprehensive .NET library that provides a unified API for working with different databases, such as SQL Server, MySQL, MariaDB, SQLite, Oracle, PostgreSQL, etc. 

 - It offers features like bulk operations, object mapping, and much more. 
 - The library allows you to write less code and get more performance out of your database operations. 
 - It is easy to configure, which helps make development faster and simpler. 
 - Dapper Plus library also supports asynchronous programming, allowing developers to use the await/async keywords for asynchronous data writing. 

## Dapper Plus - Methods

| Method | Description |
| ------ | ----------- |
| [BulkInsert](/bulk-operations/bulk-insert)| Execute a Bulk Operation for inserting thousands of entities. |
| [BulkUpdate](/bulk-operations/bulk-update)| Execute a Bulk Operation for updating thousands of entities. |
| [BulkDelete](/bulk-operations/bulk-delete)| Execute a Bulk Operation for deleting thousands of entities. |
| [BulkMerge](/bulk-operations/bulk-merge)	| `UPSERT` operations. Execute a Bulk Operation for merging thousands of entities (Update existing data and insert non-existing). |

## Dapper Plus - Getting Started

To use Dapper Plus in your application, you need to install the [NuGet Package](https://dapper-plus.net/download)

This library is not free but is the fastest way to save your data. In addition, you save development time (so money) as you don't have to write any SQL statements and unit tests for those bulk operations.

Like Dapper, Dapper Plus extends your `IDbConnection` interface but with high-performance bulk operations:

```csharp
// @nuget: Z.Dapper.Plus
using Z.Dapper.Plus;

connection.BulkInsert(customers);
connection.BulkUpdate(customers);
connection.BulkDelete(customers);
connection.BulkMerge(customers);
```

You can find more information on their [Getting Started](https://dapper-plus.net/getting-started)

## Dapper Plus - Performance

Dapper Plus significantly outperforms traditional methods for [inserting multiple rows](/saving-data/insert#dapper-insert-multiple-rows) in Dapper, providing a remarkable boost in efficiency:

- **Insert:** 75x faster, reducing time by 99% ([Online Benchmark](https://dotnetfiddle.net/zlTePU))
- **Update:** 50x faster, reducing time by 98% ([Online Benchmark](https://dotnetfiddle.net/qnbq6o))
- **Delete:** 150x faster, reducing time by 99% ([Online Benchmark](https://dotnetfiddle.net/18paED))
- **Merge:** 50x faster, reducing time by 98% ([Online Benchmark](https://dotnetfiddle.net/piaZmp))

In addition to its speed, Dapper Plus offers an extensive array of customization options for your bulk operations, allowing you to tailor data saving processes to your specific needs.