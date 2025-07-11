---
title: EF Core Bulk Upsert / Merge
description: The BulkMerge method in Entity Framework Extensions is a powerful and highly efficient way to perform bulk updates of entities within the database. It enables you to easily sync multiple records with the same data while avoiding tedious loops where one record needs to be updated at a time.
canonical: /bulk-extensions/bulk-merge
status: Published
lastmod: 2025-07-11
---

# EF Core Bulk Upsert / Merge

BulkMerge is a database operation that combines the insert and update operations into a single, efficient database call. In other words, a merge is equivalent to an `UPSERT` (Update/Insert) operation. Data that exists in the database will be updated, and non-existing data will be inserted.

A BulkMerge has other names as well such as **add or update** or **insert or update** operations.

The **easiest and fastest way** to merge data is by using the [Entity Framework Extensions](https://entityframework-extensions.net/) third-party library.

The `BulkMerge` method makes that operation very easy to use and **improves the performance by 5000%** depending more on the provider.

## Bulk Extensions Upsert / Merge

[Entity Framework Extensions](https://entityframework-extensions.net/) provides the `BulkMerge` extension method that allows you to perform bulk inserts and updates into the database in a single operation. 

 - It can reduce the complexity of writing multiple queries and adds an extra layer of performance optimization. 
 - It also supports batching which helps minimize round trips to the database by grouping commands together. 
 - Additionally, it provides helpful features such as conditional merging, making it easier to create custom logic for merging data. 

Using the `BulkMerge` method is very simple. All you need to do is pass your entities in the parameter and let the auto-mapping match properties to column names:

Here's an example of how to use the BulkMerge method in Entity Framework Core:

```csharp
using (var context = new MyDbContext())
{
    // Create a list of entities to be merged
    var authors = new List<Author>
    {
        new Author { Id = 1, Name = "John" },
        new Author { Id = 2, Name = "Jane" },
        new Author { Id = 3, Name = "Jim" }
    };

    // Use the BulkMerge method to merge the entities into the database
    context.BulkMerge(authors);
}
```

## Why You Should Use BulkMerge

The bulk merge operation allows you to perform multiple data operations at once, reducing the number of round trips to the database and improving performance. 

 - The bulk merge operation is designed to ensure data consistency by using a single transaction to perform all the operations, eliminating the risk of data inconsistencies caused by multiple, separate transactions. 
 - In databases that support this operation, the bulk merge can be much faster than executing separate insert and update operations. 
 - This feature is useful when you need to perform large-scale data operations, such as data migrations, and want to improve performance and reduce the risk of data inconsistencies.

All of these features make BulkMerge an essential tool for optimizing the performance of your data access layer. 

## Note

 - NuGet Package: [https://www.nuget.org/packages/Z.EntityFramework.Extensions.EFCore](https://www.nuget.org/packages/Z.EntityFramework.Extensions.EFCore)
 - Documentation: [Entity Framework Extensions – Bulk Merge](https://entityframework-extensions.net/bulk-merge)