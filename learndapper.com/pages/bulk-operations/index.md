---
title: Dapper Bulk Operations
description: The Dapper Plus library allows developers to easily perform bulk operations. It helps to optimize performance by reducing the number of database round trips.
canonical: /bulk-operations
status: Published
lastmod: 2024-08-30
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
| [BulkDelete](/bulk-operations/bulk-delete)| Execute a Bulk Operation for deletiong thousands of entities |
| [BulkMerge](/bulk-operations/bulk-merge)	| `UPSERT` operations. Execute a Bulk Operation for merging thousands of entities (Update existing data and insert non-existing). |

## Dapper Plus - Getting Started

To use Dapper Plus in your application, you need to install the [NuGet Package](https://dapper-plus.net/download)

This library is not free but is the fastest way to save your data. In addition, you save development time (so money) as you don't have to write any SQL statements and unit tests for those bulk operations.

You can find more information on their official [website](https://dapper-plus.net/)

Like Dapper, Dapper Plus extends your `IDbConnection` interface but with high-performance bulk operations:

```csharp
connection.BulkInsert(customers);
connection.BulkUpdate(customers);
connection.BulkDelete(customers);
connection.BulkMerge(customers);
```