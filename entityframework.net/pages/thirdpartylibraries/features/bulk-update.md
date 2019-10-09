---
PermaID: 1000021
Name: Bulk Update
---

# Bulk Update

## Introduction

BulkUpdate allows you to improve EF performance by updating multiple entities with bulk operations.

## Why BulkUpdate?

For **HUGE** performance gains, Entity Framework makes one database round-trip for each entity to update. 

So if you want to modify 10,000 entities, 10,000 database round trip will be required which is **INSANELY** slow. To use BulkUpdate, you will need to use a third-party library.

|Operations	|1,000 Entities	|2,000 Entities	|5,000 Entities|
|:----------|:----------|:----------|:----------|
|BulkUpdate	|50 ms	|55 ms	|65 ms|
|SaveChanges	|1,000 ms	|2,000 ms	|5,000 ms|

## Google Related Searches

 - [Entity Framework Insert Multiple Records](https://www.google.com/search?q=entity+framework+insert+multiple+records)
 - [Entity Framework Insert Multiple Rows](https://www.google.com/search?q=entity+framework+insert+multiple+rows)
 - [Entity Framework Add Range](https://www.google.com/search?q=entity+framework+add+range)

## StackOverflow Related Questions

 - [WebAPI EF update 30,000 rows of data is very slow](https://stackoverflow.com/questions/38925835/webapi-ef-update-30-000-rows-of-data-is-very-slow)
 - [EF 6 performance while updating multiple records with different values in same table](https://stackoverflow.com/questions/37652873/ef-6-performance-while-updating-multiple-records-with-different-values-in-same-t)
 - [Properly and efficiently update hundreds of records in sql server db table from .net c# application loop](https://stackoverflow.com/questions/37397671/properly-and-efficiently-update-hundreds-of-records-in-sql-server-db-table-from)


```csharp
// using Z.EntityFramework.Extensions; // Don't forget to include this.

// Easy to use
context.BulkUpdate(list);

// Easy to customize
context.BulkUpdate(list, bulk => bulk.BatchSize = 100);
```

## Supported Libraries

|Library	|Type	|EF Version	|Support	|Doc	|Features|
|:----------|:----------|:----------|:----------|:----------|:----------|
|[Z.EntityFramework.Extensions](/ef-extensions)	|PRO	|EF6	|< 1 Day	|Yes	| Bulk SaveChanges<br>Bulk Insert<br>Bulk Update<br>Bulk Delete<br>Bulk Merge|