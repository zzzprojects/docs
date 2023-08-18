---
title: EF Core BulkExtensions, Bulk Insert, Update, Delete, Read, SaveChanges
description: Learn to use Bulk Extensions in EF Core 7 and lower. Improve your .net core performance with bulk extensions methods.
canonical: /bulk-extensions
status: Published
lastmod: 2023-03-02
---

# Bulk Extensions With EF Core

## Getting Started

In that tutorial, you will learn how to use bulk extension methods in EF Core 7 and lower. 

Unfortunately, EF Core doesn't formal API for performing any bulk operations, so to improve your performance with bulk operations, you will need to use the [Entity Framework Extensions](https://entityframework-extensions.net/) library created by [ZZZ Projects](https://zzzprojects.com/).
 
## EF Core Bulk Extensions - Methods

| Name | Description |
| --- | --- |
| [Bulk Insert](/bulk-extensions/bulk-insert) | Extension method to insert entities in Bulk. | 
| [Bulk Insert or Update](/bulk-extensions/bulk-merge) | Extension method to insert or update entities in Bulk (Upsert operation). | 
| [Bulk Insert or Update or Delete](/bulk-extensions/bulk-synchronize) | Extension method to insert, update, or delete entities in Bulk (Sync operation). | 
| [Bulk Update](/bulk-extensions/bulk-update) | Extension method to update entities in Bulk. | 
| [Bulk Delete](/bulk-extensions/bulk-delete) | Extension method to delete entities in Bulk. | 
| [Bulk Read](#ef-core-bulk-read-extensions) | Extension method to read entities in Bulk. | 
| [Where Bulk Contains](/bulk-extensions/where-bulk-contains) | Extension method to performance a where clause in Bulk. | 
| [Bulk Save Changes](#ef-core-bulk-savechanges-extensions) | Extension method to save entities in Bulk. | 

### NuGet - EF Core Bulk Extensions

To add Entity Framework Extensions, you need the following NuGet package:  [https://www.nuget.org/packages/Z.EntityFramework.Extensions.EFCore/](https://www.nuget.org/packages/Z.EntityFramework.Extensions.EFCore/)

You must match the EF Core major version with the EF Extensions major version. For example:

- If you are using EF Core 7.x, you need to use EF Extensions 7.x
- If you are using EF Core 6.x, you need to use EF Extensions 6.x
- If you are using EF Core 5.x, you need to use EF Extensions 5.x

### GitHub - EF Core Bulk Extensions

In addition to their [site web](https://entityframework-extensions.net/), you can find some additional important information on GitHub:

- Project: [https://github.com/zzzprojects/EntityFramework-Extensions](https://github.com/zzzprojects/EntityFramework-Extensions)
- Issues Tracking: [https://github.com/zzzprojects/EntityFramework-Extensions/issues](https://github.com/zzzprojects/EntityFramework-Extensions/issues)
- Release Notes: [https://github.com/zzzprojects/EntityFramework-Extensions/releases](https://github.com/zzzprojects/EntityFramework-Extensions/releases)

## EF Core Bulk Insert Extensions

The `BulkInsert` is a method that improves performance over `SaveChanges` when you have to insert multiple entities. To perform a bulk insert, you need to call the method with the list of entities you want to insert.

```csharp
context.BulkInsert(customer);
```

See the [EF Core Bulk Insert Documentation](https://entityframework-extensions.net/bulk-insert).

## EF Core Bulk Insert or Update Extensions (Upsert)

The `BulkMerge` is a method that improves performance over `SaveChanges` when you have to insert or update multiple entities. That is an upsert operation, which will update existing entities and insert non-existing ones. To perform a bulk merge, you need to call the method with the list of entities you want to insert or update.

```csharp
context.BulkMerge(customer);
```

Learn more on [EF Core Bulk Merge Documentation](https://entityframework-extensions.net/bulk-merge).

## EF Core Bulk Insert or Update or Delete Extensions (Sync)

The `BulkSynchronize` is a method that improves performance over `SaveChanges` when you have to insert, update, or delete multiple entities. That is a sync operation, which will update existing entities, insert non-existing ones, and delete entities that are only existing in the list. To perform a bulk synchronization, you need to call the method with the list of entities you want to insert or update (without those you want to delete).

```csharp
context.BulkSynchronize(customer);
```

Learn more on [EF Core Bulk Synchronize Documentation](https://entityframework-extensions.net/bulk-synchronize).

## EF Core Bulk Update Extensions

The `BulkUpdate` is a method that improves performance over `SaveChanges` when you have to update multiple entities. To perform a bulk update, you need to call the method with the list of entities you want to update.

```csharp
context.BulkUpdate(customer);
```

Learn more on [EF Core Bulk Update](https://entityframework-extensions.net/bulk-update).

## EF Core Bulk Delete Extensions

The `BulkDelete` is a method that improves performance over `SaveChanges` when you have to delete multiple entities. To perform a bulk delete, you need to call the method with the list of entities you want to delete.

```csharp
context.BulkDelete(customer);
```

Learn more on [EF Core Bulk Delete Documentation](https://entityframework-extensions.net/bulk-delete).

## EF Core Bulk Read Extensions

The `BulkRead` is a method that allows reading data from the database with some advantage over the `Where` methods. For example, you can specify for a list which keys to use when retrieving a list.

```csharp
context.Customers.BulkRead(list, key => new { x.FirstName, x.LastName });
```

Learn more on [EF Core Bulk Read Documentation](https://entityframework-extensions.net/bulk-read).

## EF Core Bulk SaveChanges Extensions

The `BulkSaveChanges` is a method that improves performance over `SaveChanges`. Even if the `SaveChanges` is now very fast in EF Core,  when using thousands of entities, you can still have some major performance improvement by using the `BulkSaveChanges` method. To perform a save with bulk operations, you need to replace the method `SaveChanges` with the method `BulkSaveChanges`.

```csharp
context.BulkSaveChanges();
```

Learn more on [EF Core SaveChanges Documentation](https://entityframework-extensions.net/bulk-savechanges).

## Conclusion

In conclusion, the Z.EntityFramework.Extensions library is helpful when improving your CRUD performance (create, read, update, delete).

In this article, we only demonstrated the basic usage of those methods, but you can learn more about them by going directly to their respective documentation.