---
PermaID: 1000025
Name: Bulk Delete
---

# Bulk Delete

## Introduction

BulkDelete allows you to improve EF performance by deleting multiple entities with bulk operations.

## Why BulkDelete?

For **HUGE** performance gains, Entity Framework makes one database round-trip for each entity to delete. 

So if you want to modify 10,000 entities, 10,000 database round trip will be required which is **INSANELY** slow. To use BulkDelete, you will need to use a third-party library.

|Operations	|1,000 Entities	|2,000 Entities	|5,000 Entities|
|:----------|:----------|:----------|:----------|
|BulkDelete	|45 ms	|50 ms	|60 ms|
|SaveChanges	|1,000 ms	|2,000 ms	|5,000 ms|

## Google Related Searches

 - [Entity Framework Delete Multiple Records](https://www.google.com/search?q=entity+framework+delete+multiple+records)
 - [Entity Framework Delete Multiple Rows](https://www.google.com/search?q=entity+framework+delete+multiple+rows)
 - [Entity Framework Remove Range](https://www.google.com/search?q=entity+framework+remove+range)

## StackOverflow Related Questions

 - [Performance of Entity Framework](https://stackoverflow.com/questions/37204130/performance-of-entity-framework)


```csharp
// using Z.EntityFramework.Extensions; // Don't forget to include this.

// Easy to use
context.BulkDelete(list);

// Easy to customize
context.BulkDelete(list, bulk => bulk.BatchSize = 100);
```

## Supported Libraries

|Library	|Type	|EF Version	|Support	|Doc	|Features|
|:----------|:----------|:----------|:----------|:----------|:----------|
|[Z.EntityFramework.Extensions](/ef-extensions)	|PRO	|EF6	|< 1 Day	|Yes	| Bulk SaveChanges<br>Bulk Insert<br>Bulk Update<br>Bulk Delete<br>Bulk Merge|