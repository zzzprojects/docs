---
title: Dapper Bulk Merge
description: The Dapper Plus BulkMerge method allows performing UPSERT operations (update/insert) in your database and improves your performance by 5000% and more.
canonical: /bulk-operations/bulk-merge
status: Published
lastmod: 2025-06-15
---

# Bulk Merging Data With Dapper

A merge is equivalent to an `UPSERT` (Update/Insert) operation. Existing data in the database will be updated, and non-existing data will be inserted.

The **easiest and fastest way** to merge data is by using the [Dapper Plus](https://dapper-plus.net/) third-party library.

The `BulkMerge` method makes that operation very easy to use and **improves the performance by 5000%** depending on the provider ([Online Benchmark](https://dotnetfiddle.net/piaZmp)).

## Getting Started

NuGet Package: [https://www.nuget.org/packages/Z.Dapper.Plus/](https://www.nuget.org/packages/Z.Dapper.Plus/)

Documentation: [Dapper Plus – Bulk Merge](https://dapper-plus.net/bulk-merge)

## Dapper BulkMerge

**Dapper Plus** `BulkMerge` helps with Bulk Merge operations for Dapper. It allows you to perform bulk inserts, updates, and deletes into the database in a single operation. 
 - It can reduce the complexity of writing multiple queries and adds an extra layer of performance optimization. 
 - It also supports batching, which helps minimize round trips to the database by grouping commands together. 
 - Additionally, it provides helpful features such as conditional merging, making creating custom logic for merging data easier.
Using the `BulkMerge` method is very simple. All you need to do is pass your entities in the parameter and let the automapping matching properties with column names:

```csharp
// Easy to use
connection.BulkMerge(products);

// Easy to customize
connection.UseBulkOptions(options => options.MergeKeepIdentity = true)
		  .BulkMerge(products);
```

[Online Example](https://dotnetfiddle.net/v5stH2)

## Dapper BulkMerge with Global Context Mapping

The BulkMerge is also very flexible. It is possible to insert all properties and ignore some properties during the updates phase:

```csharp
// Global Mapping requires to be mapped globally only once
DapperPlusManager.Entity<Customer>("Customer_IgnoreAuditProperty")
	.IgnoreOnMergeUpdate(x => new { x.CreatedDate, x.CreatedUser });
	
// ...code...
 
connection.BulkMerge("Customer_IgnoreAuditProperty", customers);
```

## Dapper BulkMerge with Instance Context Mapping

When using a global context mapping, you need to set the mapping only once since it is global, but that’s also possible to set the mapping inside a method to specify some configuration at runtime:

```csharp
public void CustomBulkMerge<T>(List<T> entities, List<string> propertyIgnoreOnUpdateNames) where T : class
{
	var context = new DapperPlusContext();
	
	propertyIgnoreOnUpdateNames.ForEach(x =>
	{
		context.Entity<T>().IgnoreOnMergeUpdate(x);
	});
	
	context.BulkMerge(entities);
}
```

All of these features make BulkMerge an essential method for optimizing the performance of your data access layer. 

## Related Articles

- [Bulk Operations](/bulk-operations)
- [Bulk Insert](/bulk-operations/bulk-insert)
- [Bulk Update](/bulk-operations/bulk-update)
- [Bulk Delete](/bulk-operations/bulk-delete)