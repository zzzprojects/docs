---
Title: EF Core Bulk Extensions
MetaDescription: This article provides a comprehensive guide to using EFCore BulkExtensions inside EFE, a powerful library that extends Entity Framework Core to support bulk operations. Learn about its features, including bulk insert, updates, deletes, and merge (upsert). Whether you're a beginner or an experienced EFCore developer, this article will help you unlock the full potential of Z.EntityFramework.Extensions and streamline your database operations.
LastMod: 2024-08-30
---

# EF Core Bulk Extensions

## Description

EF Core Bulk Extensions are provided by the [Entity Framework Extensions](https://entityframework-extensions.net/bulk-extensions) library. Z.EntityFramework.Extensions is a third-party library that extends the functionality of EF Core. It provides additional features for working with EF Core, particularly in the area of Bulk Operations. The library provides efficient ways to perform CRUD operations such as BulkInsert, BulkUpdate, BulkDelete, BulkMerge, BulkRead, and WhereBulkContains.

Since 2019, [EF Plus depends on EFE](https://github.com/zzzprojects/EntityFramework-Plus/issues/510) to reduce maintenance cost, EF Plus stays **FREE**, however, features from EF Extensions are by default available. You can find a list of what is free and what is paid in the [download](https://entityframework-plus.net/download) section. You can also prevent using paid features by forcing the community version `EntityFrameworkManager.IsCommunity = true`


### Advantages of using Z.EntityFramework.Extensions

- **Faster Data Manipulation:** Z.EntityFramework.Extensions improve traditional database operations such as Insert, Update, Delete, and Read can be slow, especially when dealing with large datasets.
- **Flexibility:** Z.EntityFramework.Extensions provides hundreds of options to customize your bulk operations
- **Easy to use:** Z.EntityFramework.Extensions provides a simple API that is easy to use and understand.
- **Multiple EF Core versions supported:** EF Core 7, EF Core 6, EF Core 5, EF Core 3, EF Core 2 (Including EF Core 8 Preview).
- **Multiple providers supported:** SQL Server, SQLite, PostgreSQL, MySQL, MariaDB, and Oracle.

## Scenarios
 
 - [BulkInsert](#bulkinsert)
 - [BulkUpdate](#bulkupdate)
 - [BulkDelete](#bulkdelete)
 - [BulkUpsert](#bulkupsert)
 - [BulkRead](#bulkread)
 - [WhereBulkContains](#wherebulkcontains)
 
> WARNING: Features described in this section are paid and part of [Entity Framework Extensions](https://entityframework-extensions.net/bulk-extensions)
  
### BulkInsert

BulkInsert is a feature that allows you to insert multiple records into the database in a single query. This can significantly improve the performance of data insertion, especially when dealing with large datasets.

```csharp
context.BulkInsert(entities);
```

### BulkUpdate

BulkUpdate is a feature that allows you to update multiple records in the database in a single query. This can significantly improve the performance of data updates, especially when dealing with large datasets.

```csharp
context.BulkUpdate(entities);
```

### BulkDelete

BulkDelete is a feature that allows you to delete multiple records from the database in a single query. This can significantly improve the performance of data deletion, especially when dealing with large datasets.

```csharp
context.BulkDelete(entities);
```

### BulkUpsert

BulkMerge is a feature that allows you to merge multiple records into the database in a single query. The operation is also often called `BulkUpsert` because the method performs an insert and update operation. This can be useful for updating existing records or inserting new records, depending on whether the records already exist in the database or not.

```csharp
context.BulkMerge(entities);
```

### BulkRead

BulkRead is a feature that allows you to retrieve multiple records from the database in a single query by using a list of `ids` or entities. That can significantly improve the performance of data retrieval, especially when dealing with large datasets.

```csharp
context.Customers.BulkRead(ids);
```

### WhereBulkContains

BulkRead is a feature that allows you to filter multiple records from the database in a single query by using a list of `ids` or entities. That can significantly improve the performance of data retrieval, especially when dealing with large datasets. The feature is very similar to `BulkRead` but is a deferred method instead of an immediate one (so it requires `ToList()` to materialize).

```csharp
context.Customers.WhereBulkContains(ids).ToList();
```

## Conclusion

Z.EntityFramework.Extensions is a powerful library that can help you speed up data manipulation operations in your application. By providing efficient Bulk Operations such as BulkInsert, BulkUpdate, BulkDelete, BulkMerge, BulkRead, and WhereBulkContains can significantly improve the performance of data manipulation (CRUD Operations) in your application, especially when dealing with large datasets.

With a simple API and support for multiple database providers, Z.EntityFramework.Extensions is a valuable tool for any developer working with the EF Core.