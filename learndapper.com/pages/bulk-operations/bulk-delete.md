---
title: Dapper Bulk Delete
description: Dapper Plus BulkDelete allows you to delete entities from the database in a single roundtrip. It eliminates multiple trips to the db for each entity to delete.
canonical: /bulk-operations/bulk-delete
status: Published
lastmod: 2023-01-08T18:23:00Z
---

# Bulk Deleting Data With Dapper

Deleting data with Dapper is already very fast, as you can use the `IN` statement and specify a list of keys. However, in some scenarios, you might have thousands of data to delete and be limited by the number of parameters supported by SQL Server or want more flexibility.

The **fastest and easiest** way of deleting multiple data is using the [Dapper Plus](https://dapper-plus.net/) third-party library.

This library is very fast and offers hundreds of features and all other bulk operations to support the most common and complex cases.

## Getting Started

NuGet Package: [https://www.nuget.org/packages/Z.Dapper.Plus/](https://www.nuget.org/packages/Z.Dapper.Plus/)

Documentation:

- [Dapper Plus – Bulk Delete](https://dapper-plus.net/bulk-delete)
- [Dapper Plus – Mapping](https://dapper-plus.net/getting-started-mapping)

## Dapper BulkDelete

By default, you only need to pass your entities in parameter, and the library will find the primary key from your database if none has been mapped:

 - **Dapper Plus** `BulkDelete` also supports soft deletes, allowing you to mark deleted entities without actually deleting them from the database. 
 - It makes it easier to undo mistakes while maintaining a tidy database. 
 - Additionally, it supports conditional deletes, allowing you to delete entities based on specific criteria that you specify. 
 
```csharp
connection.BulkDelete(customers);
```

## Dapper BulkDelete with Global Context Mapping

In addition to being fast, the library is very flexible and allows many scenarios. For example, you have a particular scenario that you can only delete a row if, in addition to the key, the version of the `Customer` also matches the concurrency column value:

```csharp
// Global Mapping requires to be mapped globally only once
DapperPlusManager.Entity<Customer>("Customer_VersionEqual")
	.UseBulkOptions(x => x.DeleteMatchedAndConditionExpression = y => y.Version);
 
// ...code...
 
connection.BulkDelete("Customer_VersionEqual", customers);
```

## Dapper BulkDelete with Instance Context Mapping

By using an instance context mapping, you can add more flexibility by being able to specify some options at runtime:

```csharp
public void CustomBulkDelete<T>(List<T> entities, bool checkVersion) where T : IVersion
{
	var context = new DapperPlusContext();
	
	var mapping = context.Entity<T>();
	
	if(checkVersion)
	{
		mapping = mapping.UseBulkOptions(x => x.DeleteMatchedAndConditionExpression = y => y.Version);
	}
	
	context.BulkDelete(entities);
}
```

All of this combined makes Dapper Plus BulkDelete an incredibly powerful and useful method for deleting large amounts of data from the database quickly and efficiently.

## Related Articles

- [Bulk Operations](/bulk-operations)