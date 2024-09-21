---
Title: Bulk Insert | The Fastest Way in Dapper to Insert Multiple Rows
MetaDescription: Learn how to perform faster inserts in Dapper using the Bulk Insert method, understand why it's essential, and explore some common scenarios.
LastMod: 2024-09-21
---

# Bulk Insert: The Fastest Way in Dapper to Insert Multiple Rows

The Dapper Plus `BulkInsert` extension method allows you to insert multiple rows up to **75x faster** than the traditional techniques in Dapper.

```csharp
// Easy to use
connection.BulkInsert(orders);

// Easy to customize
connection.UseBulkOptions(options => options.InsertIfNotExists = true)
          .BulkInsert(orders);
```

[Online Example](https://dotnetfiddle.net/ltIqrC)

The `BulkInsert` method is not only exceptionally fast but also very easy to use and customize with all of our [available options](#options). We will explore some common options and scenarios later in this article.

## Benchmark

The traditional technique to [insert multiple rows in Dapper](https://www.learndapper.com/saving-data/insert#dapper-insert-multiple-rows) require you to write your `INSERT` statement and pass a list of entities to the [execute](https://www.learndapper.com/non-query) method:

```csharp
// TODO
connection.Execute(sql, anonymousCustomers);
```

**The problem** is that one database round-trip is required for every row that needs to be inserted, making the entire insertion operation significantly slower than if you use the Dapper Plus `BulkInsert` method.

Let's compare the performance of both techniques:

| Technique           | 50 Entities | 2,000 Entities | 5,000 Entities |
| :------------------ | -----------:| --------------:| --------------:|
| Insert (Execute)    | 1,200 ms    | 2,400 ms       | 6,000 ms       |
| BulkInsert          | 50 ms       | 55 ms          | 75 ms          |

As always, don't trust numbers on the internet! Instead, try our [online benchmark](https://dotnetfiddle.net/CqTwfr) on .NET Fiddle and see the performance difference for yourself.

The `BulkInsert` method allows you to reduce saving times by up to 99% for SQL Server when saving a large number of entities. Even for just 50 rows, it can reduce saving times by 88%, which can dramatically improve the user experience you provide to your clients.

The performance varies depending on the provider, but you should always see a significant positive impact.

## Getting Started with Bulk Insert

To get started, we recommend first reading our [Bulk Extensions Methods](/bulk-extensions-methods) articles. Here is a recap:

- You can insert asynchronously with the `BulkInsertAsync` method.
- You can [chain](/bulk-extensions-methods#chaining) operations with the `AlsoBulkInsert` and `ThenBulkInsert` methods.
- You can use `BulkInsert` from a connection, transaction, or a new [Dapper Plus Context](/dapper-plus-context).
- You can utilize the `BulkInsert` method with multiple different [DataSources](/datasource).

```csharp
// Example code will be provided here
```

[Online Example](https://dotnetfiddle.net/ltIqrC)

## Common Options / Scenarios

In this section, we will explore some common options and scenarios that developers often use with the `BulkInsert` method:

- InsertIfNotExists
- InsertKeepIdentity

For more options, refer to our [list of options](/options) documentation.

### InsertIfNotExists

This option ensures that only new entities that do not already exist in the database are inserted. It is great for maintaining data integrity and avoiding duplicate entries. This option requires you to set a key during the [mapping](/mapping) process or use the `ColumnPrimaryKeyExpression` or `ColumnPrimaryKeyNames` options.

```csharp
// Example code demonstrating InsertIfNotExists
```

[Online Example](https://dotnetfiddle.net/ltIqrC)

### InsertKeepIdentity

This option allows you to insert specific values into an identity column from your entities. This is useful when you want to maintain the same identity values as in your source data.

```csharp
// Example code demonstrating InsertKeepIdentity
```

[Online Example](https://dotnetfiddle.net/ltIqrC)

## Conclusion

In this article, we've explored the `BulkInsert` method, discussed why it is beneficial, and highlighted a few common scenarios where it can be particularly effective.

The `BulkInsert` method is not only advantageous for performance when dealing with large datasets but is also valuable even when inserting a single row. It saves development time as it eliminates the need to write and maintain SQL queries.