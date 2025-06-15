---
title: Dapper Bulk Update
description: The Dapper Plus BulkUpdate method allows executing bulk updates in a single database call. Performance can be improved by up 5000% and more.
canonical: /bulk-operations/bulk-update
status: Published
lastmod: 2025-06-15
---

# Bulk Updating Data With Dapper

Dapper provides the [Execute method for updating data](/non-query#dapper-update). However, a database roundtrip is required for every data you update. The more database roundtrips you do, the longer it will take to complete all your updates.

It is possible to dramatically reduce the number of database roundtrips by using the `BulkUpdate` method from the [Dapper Plus](https://dapper-plus.net/) third-party library.

Performance can be **improved by up to 5000%** and more depending on the provider ([Online Benchmark](https://dotnetfiddle.net/qnbq6o)).

## Getting Started

NuGet Package: [https://www.nuget.org/packages/Z.Dapper.Plus/](https://www.nuget.org/packages/Z.Dapper.Plus/)

Documentation: [Dapper Plus – Bulk Update](https://dapper-plus.net/bulk-update)

## Dapper BulkUpdate

**Dapper Plus** `BulkUpdate` method allows you to update multiple entities in a single round trip. It is more efficient than updating each entity one by one and can help improve your application's performance by reducing the number of database calls. 

 - The `BulkUpdate` method allows you to specify which properties to update.
 - With the `BulkUpdate` method, you can easily update thousands of records at once and make sure that your data is always up-to-date. 

To perform a `BulkUpdate`, you need to pass your entities in the parameter:

```csharp
// Easy to use
connection.BulkUpdate(products);

// Easy to customize
connection.UseBulkOptions(options => options.BatchSize = 1000)
		  .BulkUpdate(products);
```

[Online Example](https://dotnetfiddle.net/iezfmD)

## Dapper BulkUpdate with Global Context Mapping

An entity type can be mapped more than once using a mapping key. For example, for a specific scenario, you only want to update some properties:

```csharp
// Global Mapping requires to be mapped globally only once
DapperPlusManager.Entity<Customer>("Customer_CustomMap")
	.Map(nameof(Customer.Email))
	.Map(nameof(Customer.Name));
 
// ...code...
 
connection.BulkUpdate("Customer_CustomMap", customers);
```

## Dapper BulkUpdate with Instance Context Mapping

By using an instance context mapping, you can choose to map your entity type at runtime:

```csharp
public void CustomBulkUpdate<T>(List<T> entities, List<string> propertyNames) where T : class
{
	var context = new DapperPlusContext();
	
	propertyNames.ForEach(x =>
	{
		context.Entity<T>().Map(x);
	});
	
	context.BulkUpdate(entities);
}
```

## Related Articles

- [Bulk Operations](/bulk-operations)
- [Bulk Insert](/bulk-operations/bulk-insert)
- [Bulk Delete](/bulk-operations/bulk-delete)
- [Bulk Merge (Upsert)](/bulk-operations/bulk-merge)
