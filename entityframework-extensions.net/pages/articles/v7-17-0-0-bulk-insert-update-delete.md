---
Title: v7.17.0.0 - Bulk Insert Improvement for EF Core 7
MetaDescription: Learn what is new in Entity Framework Extensions for EF Core 7 and what the Microsoft new features called BulkUpdate and BulkDelete really are.
LastMod: 2023-11-30
---

# EF Extensions for EF Core 7 - What is New?

[EF Core 7](https://github.com/dotnet/efcore/releases/tag/v7.0.0) was finally released on November 8, 2022, along with some new features and confusion.

Entity Framework Extensions officially start to support EF Core 7 starting from v7.17.0.0

If you are confused with our versioning, please refer to [EFCore-Pinned Versioning](/efcore-pinned-versioning) convention.

## EF Core 7 and Bulk Insert

Through the year, multiple improvements have been made to our [EF Core Bulk Insert](/bulk-insert) method and also other [BulkExtensions](/bulk-extensions) methods such as:

- Major IncludeGraph revamp (better performance with less memory usage) ([v7.100.0](/v7-100-0-0-include-graph))
- Added QueryHint option for SQL Server ([v7.21.1](https://github.com/zzzprojects/EntityFramework-Extensions/releases/tag/7.21.1))
- Removed Newtonsoft.Json dependency ([v7.20.0](https://github.com/zzzprojects/EntityFramework-Extensions/releases/tag/7.20.0))
- Performance Improvement for `Owned` and `Shadow` value ([v7.18.3](https://github.com/zzzprojects/EntityFramework-Extensions/releases/tag/7.18.3))
- Added support to [TPC](https://www.learnentityframeworkcore.com/inheritance/table-per-concrete) ([v7.18.1](https://github.com/zzzprojects/EntityFramework-Extensions/releases/tag/7.18.1))
- Added support to entity type with nullable owned types ([v7.17.0](https://github.com/zzzprojects/EntityFramework-Extensions/releases/tag/7.17.0))

And many more.

Even with all those improvements, we always make sure that our library stays easy to use for everyone:

```csharp
// Easy to use
context.BulkInsert(customers);

// Easy to customize
context.BulkInsert(invoices, options => options.IncludeGraph = true);
```

## EF Core 7 now supports Bulk Update and Bulk Delete, oh really?

EF Core 7 added **A LOT of confusion** by marketing their newly [ExecuteUpdate](https://learn.microsoft.com/en-us/ef/core/saving/execute-insert-update-delete#executeupdate) and [ExecuteDelete](https://learn.microsoft.com/en-us/ef/core/saving/execute-insert-update-delete#executedelete) as being a `BulkUpdate` and `BulkDelete` which is not. Even their documentation doesn't mention the word `Bulk` once...

But what exactly do these two new methods do?

- The `ExecuteUpdate` is similar to our [UpdateFromQuery](/update-from-query) method that allows to update rows directly in the database using a query.
- The `ExecuteDelete` is similar to our [DeleteFromQuery](/delete-from-query) method that allows to delete rows directly in the database using a query.

You can feel free to use either our method or Microsoft method to achieve this behavior. While being very similar, they have a few differences, such as the way they specify property values to update.

So, in other words, they don't cover at all or have anything in common with our [BulkUpdate](/bulk-update) and [BulkDelete](/bulk-delete) methods:

- **BulkUpdate**: Update in bulk all rows that match your entities list (so every entity can have different values).
- **BulkDelete**: Delete in bulk all rows that match your entities list.

So be careful when seeing that EF Core supports bulk operations, as a lot of articles and videos will use those big SEO keywords, adding even more confusion.

## Conclusion

EF Core 7 is a big success for the Microsoft Team. They worked hard indeed to add support for `TPC` (Table Per Hierarchy), which we believe was the hardest inheritance to add and to improve the performance overall. The only negative point was to promote their 2 new methods as `BulkUpdate` and `BulkDelete`. 

As for ZZZ Projects, making sure that [Entity Framework Extensions](https://entityframework-extensions.net/) support EF Core 7 was also a big challenge as we kept learning new features that we were not even aware were supported. However, our library is improving daily, making it the most advanced and customizable library for bulk operations.
