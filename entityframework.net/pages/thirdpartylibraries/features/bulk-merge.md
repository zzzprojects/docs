---
PermaID: 1000023
Title: Entity Framework Bulk Merge - Learn who supports this feature
MetaDescription: Unlock the power of Entity Framework by learning which libraries support the Bulk Merge feature.
LastMod: 2025-06-19
Tags: third-party-feature
---

# Entity Framework Bulk Merge: Discover who supports this feature

## Introduction

BulkMerge allows you to improve EF performance by merging multiple entities with bulk operations.

## Why BulkMerge?

For **HUGE** performance gains, Entity Framework makes one database round-trip for each entity to insert/update.

So if you want to modify 10,000 entities, 10,000 database round-trips will be required, which is **INSANELY** slow. To use BulkMerge, you will need to use a third-party library.

|Operations	|1,000 Entities	|2,000 Entities	|5,000 Entities|
|:----------|:----------|:----------|:----------|
|BulkMerge	|65 ms	|80 ms	|110 ms|
|SaveChanges	|1,000 ms	|2,000 ms	|5,000 ms|

## Google Related Searches

 - [Entity Framework Add or Update](https://www.google.com/search?q=entity+framework+add+or+update)
 - [Entity Framework Insert or Update](https://www.google.com/search?q=entity+framework+insert+or+update)
 - [Entity Framework Upsert](https://www.google.com/search?q=entity+framework+upsert)

## StackOverflow Related Questions

 - [Entity Framework update/insert multiple entities](https://stackoverflow.com/questions/39656794/entity-framework-update-insert-multiple-entities)
 - [Insert/Update bulk data to sql table in entity framework](https://stackoverflow.com/questions/37749818/insert-update-bulk-data-to-sql-table-in-entity-framework)


```csharp
// using Z.EntityFramework.Extensions; // Don't forget to include this.

// Easy to use
context.BulkMerge(list);

// Easy to customize
context.BulkMerge(list, bulk => bulk.BatchSize = 100);
```

## Supported Libraries

|Library	|Type	|EF Version	|Support	|Doc	|Features|
|:----------|:----------|:----------|:----------|:----------|:----------|
|[Z.EntityFramework.Extensions](/ef-extensions)	|PRO	|EF6	|< 1 Day	|Yes	| Bulk SaveChanges<br>Bulk Insert<br>Bulk Update<br>Bulk Delete<br>Bulk Merge|