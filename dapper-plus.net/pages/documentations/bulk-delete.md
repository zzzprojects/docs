---
Title: Bulk Delete | The Easiest Way in Dapper to Delete Multiple Rows
MetaDescription: Learn how to perform easier delete in Dapper using the Bulk Delete method, understand why it's essential, and explore some common scenarios.
LastMod: 2024-09-21
---

# Bulk Delete: The Easiest Way in Dapper to Delete Multiple Rows

The Dapper Plus `BulkDelete` extension method allows you to delete multiple rows from your database.

```csharp
// Easy to use
connection.BulkDelete(products);

// Easy to customize
var resultInfo = new ResultInfo();
connection.UseBulkOptions(options => {
	options.UseRowsAffected = true;
	options.ResultInfo = resultInfo; })
	.BulkDelete(products);
```

[Online Example](https://dotnetfiddle.net/p7L99k)

The `BulkDelete` method simplifies the process of deleting your entities, making it easier to write and maintain. We will explore some common options and scenarios later in this article.

## Benchmark

The traditional technique to delete multiple rows in Dapper when using a surrogate key requires you to write a `DELETE` statement and pass a list of entities to the [execute](https://www.learndapper.com/non-query) method:

```csharp
connection.Execute(@"DELETE Product WHERE ProductID = @ProductID", products);
```

However, if you have a unique key, you can also use the [Where IN Parameter](https://www.learndapper.com/parameters#dapper-where-in-parameters) to make it significantly faster for a low number of rows:

```csharp
connection.Execute(@"DELETE Product WHERE ProductID IN @ProductIDs", new { ProductIDs = products.Select(x => x.ProductID).ToList() });
```

Let's compare the performance of the three techniques:

| Technique              | 50 Entities | 1,000 Entities | 2,000 Entities |
| :--------------------- | -----------:| --------------:| --------------:|
| Delete (Execute)       | 180 ms      | 3300 ms        | 6,600 ms       |
| Delete (IN Parameter)  | 15 ms       | 200 ms         | 650 ms          |
| BulkDelete             | 25 ms       | 35 ms          | 45 ms          |

You can try this [online benchmark](https://dotnetfiddle.net/18paED) directly on .NET Fiddle.

As the benchmark shows, using the [Where IN Parameter](https://www.learndapper.com/parameters#dapper-where-in-parameters) solution is already faster than our library for simple cases. This method is effective if you do not use a surrogate key with a few number of entities. However, in the case of a surrogate key, our `BulkDelete` method can reduce deletion times by up to 99% for SQL Server when deleting a large number of entities.

## Getting Started with Bulk Delete

To get started, please read our [Bulk Extensions Methods](/bulk-extensions-methods) documentation, as all main concepts are discussed there.

Here is a recap:

- You can delete asynchronously with the `BulkDeleteAsync` method.
- You can [chain](/bulk-extensions-methods#chaining) operations with the `AlsoBulkDelete` and `ThenBulkDelete` methods.
- You can use `BulkDelete` from a connection, transaction, or a new [Dapper Plus Context](/dapper-plus-context).
- You can utilize the `BulkDelete` method with multiple different [DataSources](/datasource).

## Common Options / Scenarios

In this section, we will explore some common options and scenarios that developers often use with the `BulkDelete` method:

- Conditional Delete
- Rows Affected

For more options, refer to our [list of options](/options) documentation.

### Conditional Delete

If your table also has a rowversion column, you might only want to delete rows that have a corresponding rowversion. The `DeleteMatchedAndConditionNames` and `DeleteMatchedAndConditionExpression` options allow you to delete only rows that match a specific condition.

```csharp
DapperPlusManager.Entity<Product>()
	.Table("Product")
	.Identity(x => x.ProductID)
	.UseBulkOptions(x => {
		x.DeleteMatchedAndConditionExpression = y => new { y.Version };
	});
	
var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServer());

connection.BulkDelete(products);
```

[Online Example](https://dotnetfiddle.net/LNO8Mj)

### Rows Affected

By default, Dapper Plus does not return the number of rows affected as it can decrease performance in some scenarios. However, you can enable this feature with the `UseRowsAffected` option and view the results from the `ResultInfo` property.

```csharp
var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServer());

var resultInfo = new ResultInfo();

connection.UseBulkOptions(options => {
	options.UseRowsAffected = true;
	options.ResultInfo = resultInfo; })
	.BulkDelete(products);
```

[Online Example](https://dotnetfiddle.net/WOFkM6)

## Conclusion

In this article, we've explored the `BulkDelete` method. We have seen that unlike the [BulkInsert](/bulk-insert), which is consistently faster than traditional insertion methods, the `BulkDelete` might not always be the best option unless you have a surrogate key.

However, the syntax of the method surely makes it easier to use and ensures consistency throughout your project if you are already using other bulk operation methods. This can simplify development and maintenance, providing a uniform approach to handling data operations across your application.