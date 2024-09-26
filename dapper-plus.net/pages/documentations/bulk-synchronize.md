---
Title: Bulk Synchronize | The Best Way in Dapper to Synchronize Multiple Rows
MetaDescription: Learn how to perform faster synchronization in Dapper using the Bulk Synchronize method, understand why it's essential, and explore some common scenarios.
LastMod: 2024-09-21
---

# Bulk Synchronize: The Best Way in Dapper to Synchronize Multiple Rows

The Dapper Plus `BulkSynchronize` extension method acts as a **MIRROR** operation; it makes your table exactly reflect your data source. This means it will **UPDATE** existing rows, **INSERT** new rows that do not exist in the database, and **DELETE** rows that are no longer present in your data source.

```csharp
// Easy to use
connection.BulkSynchronize(products);

// Easy to customize
connection.UseBulkOptions(options => options.BatchSize = 1000)
		  .BulkSynchronize(products);
```

[Online Example](https://dotnetfiddle.net/V2iLNI)

The `BulkSynchronize` method may be less frequently used, but it remains one of the most powerful tools in the Dapper Plus arsenal, allowing you to create an exact mirror of an entire table or just a portion of it, as we will explore later in this article.

## Benchmark

There isn't a traditional technique in Dapper for fully synchronizing a table—essentially, a mirror operation is not as straightforward as performing [Insert](/bulk-insert) or [Merge](/bulk-merge) operations.

However, let's examine the performance of our `BulkSynchronize` method:

| Technique        | 50 Entities | 1,000 Entities | 2,000 Entities |
| :--------------- | -----------:| --------------:| --------------:|
| BulkSynchronize  | 30 ms       | 90 ms          | 140 ms         |

As always, don't just take our word for it. Try our [online benchmark](https://dotnetfiddle.net/0BOMyw) on .NET Fiddle to see the performance difference for yourself and verify these results firsthand.

## Getting Started with Bulk Synchronize

To effectively begin using the `BulkSynchronize` method, we recommend first familiarizing yourself with our comprehensive [Bulk Extensions Methods](/bulk-extensions-methods) articles.

Here is a quick recap:

- **Asynchronous Synchronization**: You can synchronize data asynchronously with the `BulkSynchronizeAsync` method.
- **Chaining Operations**: Enhance workflow efficiency by chaining operations with the `AlsoBulkSynchronize` and `ThenBulkSynchronize` methods.
- **Flexible Usage**: Use `BulkSynchronize` from a connection, transaction, or leverage a new [Dapper Plus Context](/dapper-plus-context) for greater control.
- **Multiple Data Sources**: The `BulkSynchronize` method supports synchronization across various [DataSources](/datasource), enhancing its adaptability.

```csharp
// Example code will be provided here to demonstrate the use of BulkSynchronize
```

[Online Example](https://dotnetfiddle.net/ltIqrC)

This setup guides you through the initial steps to effectively use `BulkSynchronize`, emphasizing its flexibility and power in handling complex data synchronization scenarios.

## Common Options / Scenarios

In this section, we explore some common options and scenarios that developers frequently use with the `BulkSynchronize` method:

- **Synchronize Only a Subset of Your Data**
- **SynchronizeSoftDeleteFormula**

For a comprehensive list of options, please refer to our [options documentation](/options).

### Synchronize Only a Subset of Your Data

Suppose your table has a column named `StoreID` that acts like a tenant identifier. You can synchronize only a specific store by using the `ColumnSynchronizeDeleteKeySubsetExpression` or `ColumnSynchronizeDeleteKeySubsetNames` option. This ensures that only data from your data source for a specified store is synchronized, leaving all other store data unmodified.

```csharp
DapperPlusManager.Entity<Product>()
	.Table("Product")
	.Identity(x => x.ProductID)
	.UseBulkOptions(options => options.ColumnSynchronizeDeleteKeySubsetExpression = x => new { x.StoreID });
	
var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServer());
	
connection.BulkSynchronize(products);
```

[Online Example](https://dotnetfiddle.net/J4ogEk)

### SynchronizeSoftDeleteFormula

If you prefer performing a soft delete instead of a hard delete, you can use the `SynchronizeSoftDeleteFormula`. This option allows you to update non-existing rows based on your formula, effectively enabling soft deletion where data is marked as inactive instead of being removed.

```csharp
DapperPlusManager.Entity<Product>()
	.Table("Product")
	.Identity(x => x.ProductID)
	.UseBulkOptions(options => options.SynchronizeSoftDeleteFormula = "IsSoftDeleted = 1");
	
var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServer());
	
connection.BulkSynchronize(products);
```

[Online Example](https://dotnetfiddle.net/Q5uzy3)

These options enhance the flexibility of the `BulkSynchronize` method, making it suitable for a variety of data handling scenarios.

## Conclusion

In this article, we've explored the `BulkSynchronize` method, discussed why it is beneficial, and highlighted a few common scenarios where it can be particularly effective.

The `BulkSynchronize` method is unique and incredibly powerful. While not all projects may require such a feature, for those that do, having access to a pre-coded and thoroughly tested method can be a significant advantage. This saves you the time and effort of having to develop and debug a complex synchronization process on your own.