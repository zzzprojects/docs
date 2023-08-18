---
title: EF Core Bulk Delete Extensions
description: Entity Framework Extensions BulkDelete allows you to delete entities from the database in a single roundtrip. This is especially useful when deleting large amounts of entities from the database in one go, as it eliminates multiple trips to the database for each entity that needs to be deleted.
canonical: /bulk-extensions/bulk-delete
status: Published
lastmod: 2023-03-02
---

# EF Core Bulk Delete

EF Core provides the `Remove` and `RemoveRange` methods for deleting entities. However, it may not be the best option for deleting large amounts of data from a database. 

The **fastest and easiest** way of deleting multiple entities is by using the [Entity Framework Extensions](https://entityframework-extensions.net/) third-party library.

This library is very fast and offers hundreds of features and all other bulk operations to support the most common cases and many complex ones.

## Bulk Extensions Delete

[Entity Framework Extensions](https://entityframework-extensions.net/) provides the `BulkDelete` extension method can be used to efficiently delete large amounts of data from a database.

By default, you only need to pass your entities in the parameter, and the library will find the primary key from your database.

```csharp
using (var context = new MyDbContext())
{
    context.BulkDelete(customerToDeletes);
}
```

In this example, the `BulkDelete` method is used to delete all customers from `customerToDeletes`. The `BulkDelete` method is much faster than deleting entities one by one, so it's ideal for large-scale deletions.

## Note

 - NuGet Package: [https://www.nuget.org/packages/Z.EntityFramework.Extensions.EFCore](https://www.nuget.org/packages/Z.EntityFramework.Extensions.EFCore)
 - Documentation: [Entity Framework Extensions – Bulk Delete](https://entityframework-extensions.net/bulk-delete)
