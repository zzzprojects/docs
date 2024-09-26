---
Title: Bulk Merge | The Fastest Way in Dapper to Upsert Multiple Rows
MetaDescription: Learn how to perform faster "add or update"/"upsert" in Dapper using the Bulk Merge method, understand why it's essential, and explore some common scenarios.
LastMod: 2024-09-21
---

# Bulk Merge: The Fastest Way in Dapper to Upsert Multiple Rows

The Dapper Plus `BulkMerge` extension method performs an `Add or Update` operation, more commonly known as an [Upsert](https://en.wikipedia.org/wiki/Merge_(SQL)). This method updates existing rows and inserts non-existing rows in your database seamlessly.

```csharp
// Easy to use
connection.BulkMerge(products);

// Easy to customize
connection.UseBulkOptions(options => options.MergeKeepIdentity = true)
		  .BulkMerge(products);
```

[Online Example](https://dotnetfiddle.net/v5stH2)

`BulkMerge` not only merges your entities at an extremely fast rate but also allows you to quickly customize the process with [hundreds of options](/options), eliminating the need to remember complex SQL syntax.

## Benchmark

The traditional technique to [add or update multiple rows in Dapper](https://www.learndapper.com/saving-data/insert#dapper-insert-multiple-rows) requires you to write your `INSERT` statement and pass a list of entities to the [execute](https://www.learndapper.com/non-query) method:

```csharp
connection.Execute(@"
MERGE INTO Product AS Target
USING (SELECT @ProductID AS ProductID, @Name AS Name, @Description AS Description,
              @Column1 AS Column1, @Column2 AS Column2, @Column3 AS Column3,
              @Column4 AS Column4, @Column5 AS Column5, @Column6 AS Column6,
              @Column7 AS Column7, @Column8 AS Column8, @Column9 AS Column9) AS Source
ON Target.ProductID = Source.ProductID
WHEN MATCHED THEN
    UPDATE SET
        Target.Name = Source.Name,
        Target.Description = Source.Description,
        Target.Column1 = Source.Column1,
        Target.Column2 = Source.Column2,
        Target.Column3 = Source.Column3,
        Target.Column4 = Source.Column4,
        Target.Column5 = Source.Column5,
        Target.Column6 = Source.Column6,
        Target.Column7 = Source.Column7,
        Target.Column8 = Source.Column8,
        Target.Column9 = Source.Column9
WHEN NOT MATCHED BY TARGET THEN
    INSERT (Name, Description, Column1, Column2, Column3, Column4, Column5, Column6, Column7, Column8, Column9)
    VALUES (Source.Name, Source.Description, Source.Column1, Source.Column2, Source.Column3, Source.Column4, Source.Column5, Source.Column6, Source.Column7, Source.Column8, Source.Column9);
			", products);
```

**The problem** is similar to what we have observed in our [Bulk Insert Benchmark](/bulk-insert#benchmark); one database round-trip is required for every row that needs to be updated, making the entire operation significantly slower than if you use the Dapper Plus `BulkMerge` method. Additionally, the syntax for a traditional upsert operation can be less intuitive, often requiring a refresher even for experienced developers (myself included—I often have to look it up on Google!).

Let's compare the performance of both techniques:

| Technique        | 50 Entities | 1,000 Entities | 2,000 Entities |
| :--------------- | -----------:| --------------:| --------------:|
| Merge (Execute)  | 175 ms      | 3500 ms        | 7000 ms        |
| BulkMerge        | 30 ms       | 90 ms          | 140 ms         |

As demonstrated with other bulk operations, you can try this [online benchmark](https://dotnetfiddle.net/piaZmp) on .NET Fiddle.

The `BulkMerge` method can reduce saving times by up to 99% for SQL Server when handling a large number of entities. Additionally, it significantly cuts down the time spent writing and maintaining code due to the complexity of the `MERGE` statement compared to traditional insert or update statements.

## Getting Started with Bulk Merge

To get started with the `BulkMerge` method, please read our comprehensive [Bulk Extensions Methods](/bulk-extensions-methods) article for all the necessary information.

Here is a recap:

- **Asynchronous Upsert**: You can perform upsert operations asynchronously with the `BulkMergeAsync` method.
- **Chaining Operations**: Enhance workflow efficiency by chaining operations with the `AlsoBulkMerge` and `ThenBulkMerge` methods.
- **Versatile Usage**: Utilize `BulkMerge` from a connection, transaction, or a new [Dapper Plus Context](/dapper-plus-context).
- **Multiple Data Sources**: The `BulkMerge` method can be applied across various [DataSources](/datasource), enhancing its adaptability.

## Common Options / Scenarios

In this section, we will explore some common options and scenarios that developers often use with the `BulkMerge` method:

- Ignore Properties on Insert or Update Only
- Conditional Update
- Merge Keep Identity

For a more comprehensive list of options, please refer to our [options documentation](/options).

### Ignore Properties on Insert or Update Only

This option is often very useful for audit properties such as `CreatedDate` and `LastUpdatedDate`.

- **Ignore on Merge Insert**: Use `IgnoreOnMergeInsertExpression` or `IgnoreOnMergeInsertNames` to exclude `LastUpdatedDate` during the insert phase.
- **Ignore on Merge Update**: Use `IgnoreOnMergeUpdateExpression` or `IgnoreOnMergeUpdateNames` to exclude `CreatedDate` during the update phase.

```csharp
DapperPlusManager.Entity<Product>()
	.Table("Product")
	.Identity(x => x.ProductID)
	.UseBulkOptions(x => {
		x.IgnoreOnMergeInsertExpression = y => new { y.LastUpdatedDate };
		x.IgnoreOnMergeUpdateExpression = y => new { y.CreatedDate };
	});
	
var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServer());

connection.BulkMerge(products);
```

[Online Example](https://dotnetfiddle.net/KnXMdZ)

### Conditional Update

Similar to what we have seen for the [Bulk Update - Conditional Update](/bulk-update#conditional-update), this option allows you to update rows only if at least one value has been modified.

Use `MergeMatchedAndOneNotConditionNames` or `MergeMatchedAndOneNotConditionExpression` to specify which properties must differ before an update is applied.

```csharp
DapperPlusManager.Entity<Product>()
	.Table("Product")
	.Identity(x => x.ProductID)
	.UseBulkOptions(x => {
		x.MergeMatchedAndOneNotConditionExpression = y => new { y.Name };
	});
	
var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServer());

connection.BulkMerge(products);
```

[Online Example](https://dotnetfiddle.net/UbOQVY)

### Merge Keep Identity

This option allows rows that will be inserted to retain specific values in an identity column from your entities. This is particularly useful when you want to maintain the same identity values as in your source data.

```csharp
var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServer());

connection.UseBulkOptions(options => options.MergeKeepIdentity = true)
		  .BulkMerge(products);
```

[Online Example](https://dotnetfiddle.net/72fytT)

## Conclusion

In this article, we've explored the `BulkMerge` method, discussed its benefits, and highlighted a few common scenarios where it can be particularly effective.

The three major advantages of `BulkMerge` are, without a doubt:
- **Performance**: It processes large datasets rapidly, offering a significant speed advantage.
- **Clear Syntax**: The method simplifies complex SQL upsert operations, making them easier to write and understand.
- **Flexibility**: It offers a wide range of customization options to tailor the functionality to your specific needs.

Being able to write an upsert statement in just a few seconds and customize it with all [available options](/options) will undoubtedly always benefit you.