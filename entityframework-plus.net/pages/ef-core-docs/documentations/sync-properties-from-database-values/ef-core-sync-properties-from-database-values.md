---
Permalink: ef-core-sync-properties-from-database-values
Name: Sync Properties from Database Values
LastMod: 2024-02-18
---

# Sync Properties from Database Values

## Description

The `SyncPropertiesFromDatabaseValues` method allows you to synchronize one or more properties from a list using the database value.

Let's explain with the following example:

```csharp
var products = GetProductToImport();

using (var context = new EntityContext())
{
	context.SyncPropertiesFromDatabaseValues(products, options =>
	{
		options.ColumnPrimaryKeyExpression = x => new { x.Sku };
		options.SyncPropertyExpression = x => new { x.ProductID };
	});
	
	context.BulkMerge(products);
}
```

In this example, we want to [bulk merge (Upsert)](https://entityframework-extensions.net/bulk-merge) a list of products. However, the method `GetProductToImport` only provides information like the `Sku`, `Name`, and `Description`. The importation currently doesn't know the `ProductID` as it's coming from an external database.

Since the `Sku` is unique to a product, we can use it as our key to retrieve the `ProductID`.

The following line tells the method to use the `Sku` as the key:

```csharp
options.ColumnPrimaryKeyExpression = x => new { x.Sku };
```

And the following line lets the method know which properties it should retrieve from the database:

```csharp
options.SyncPropertyExpression = x => new { x.ProductID };
```

After the method is executed, we can now easily use the `BulkMerge` method, which will use the `ProductID` that we just retrieved to see if the product already exists or not.

## Options

The method currently supports the following options:

| Name | Description |
| :--- | :---------- |
| ColumnPrimaryKeyExpression | This option allows you to choose which key to use through an expression. |
| ColumnPrimaryKeyNames | This option allows you to choose which key to use by passing a list of strings. |
| PrimaryKeyAndFormula | This option allows you to add any `SQL` text to filter even more what will be joined. |
| SyncPropertyExpression | This option allows you to choose which property to synchronize through an expression. |
| SyncPropertyNames | This option allows you to choose which property to synchronize by passing a list of strings. |
| IgnorePropertyExpression | This option allows you to choose which property to NOT synchronize through an expression. All other properties will be synchronized. |
| IgnorePropertyNames | This option allows you to choose which property to NOT synchronize by passing a list of strings. All other properties will be synchronized. |

## Real Life Scenarios

### Concurrency

One common use of this method is when you want to quickly solve [concurrency issues with the Client Wins](https://entityframework-extensions.net/concurrency#client-wins) scenario to [handle concurrency conflicts](https://learn.microsoft.com/en-us/ef/core/saving/concurrency).

If the concurrency token is currently different from the database, you have to use [GetDatabaseValues](https://learn.microsoft.com/en-us/dotnet/api/microsoft.entityframeworkcore.changetracking.entityentry.getdatabasevalues) for every entity only to retrieve the latest concurrency value.

Using `SyncPropertiesFromDatabaseValues`, you can retrieve all values at once.

### Retrieving ID

As shown in the example in the description, when using methods such as [BulkUpdate](https://entityframework-extensions.net/bulk-update) and [BulkDelete](https://entityframework-extensions.net/bulk-delete) from Entity Framework Extensions, you do not always have the ID.

While it's possible within Entity Framework Extensions to set a [ColumnPrimaryKeyExpression](https://entityframework-extensions.net/column-primary-key-expression), sometimes it's better to directly use the real key for performance.