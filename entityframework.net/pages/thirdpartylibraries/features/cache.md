---
PermaID: 1000020
Title: Entity Framework Cache - Learn who supports this feature
MetaDescription: Unlock the power of Entity Framework by learning which libraries support the Query Cache feature.
LastMod: 2025-06-19
Tags: third-party-feature
---

# Entity Framework Cache: Discover who supports this feature

## Introduction

Cache library allows you to cache the result of a LINQ query, usually in the memory, and re-use the cached result for subsequent calls.

## Why Cache?

### Common Scenarios:

 - Cache read-only table like countries and states
 - Cache rarely modified table (most libraries allow cache expiration)
 - Cache information related to the logged-in user

## Google Related Searches

 - [Entity Framework Caching Data](https://www.google.com/search?q=entity+framework+caching+data)
 - [Entity Framework First Level Cache](https://www.google.com/search?q=entity+framework+first+level+cache)
 - [Entity Framework Second Level Cache](https://www.google.com/search?q=entity+framework+second+level+cache)

## StackOverflow Related Questions

 - [Entity Framework flexible cache](https://stackoverflow.com/questions/38527253/entity-framework-flexible-cache)
 - [Entity Framework second level cache](https://stackoverflow.com/questions/35549009/entity-framework-second-level-cache)



```csharp
// using Z.EntityFramework.Plus; // Don't forget to include this.
var ctx = new EntitiesContext();

// The first call performs a database round trip
var countries1 = ctx.Countries.FromCache().ToList();

// Subsequent calls will take the value from the memory instead
var countries2 = ctx.Countries.FromCache().ToList();
```

## Supported Libraries

|Library	|Type	|EF Version	|Support	|Doc	|Features|
|:----------|:----------|:----------|:----------|:----------|:----------|
|[Z.EntityFramework.Classic](/ef-classic)	|FREE/PRO	|EF6|< 1 Day	|Yes	| Bulk SaveChanges<br>Bulk Insert<br>Bulk Update<br>Bulk Delete<br>Bulk Merge<br>Batch Delete<br>Batch Update<br>Cache<br>Deferred Query<br>Future|
|[Z.EntityFramework.Plus](/ef-plus)	|FREE	|EF5<br>EF6<br>EF Core|	< 1 Day	|Yes    |Audit<br>Batch Delete<br>Batch Update<br>Cache<br>Deferred Query<br>Filter<br>Future<br>Include Filter<br>Include Optimized|
|[EntityFramework.Cache](/ef-cache)	|FREE	|EF6	|< 2 Days	|No	    | Cache |

## Unsupported Libraries

Use these libraries at your risk!

|Library	|Type	|EF Version	|Support	|Doc	|Features |
|:--------- |:--------- |:--------- |:--------- |:--------- |:--------- |
|[EntityFramework.Extended](/ef-extended)	|FREE	|EF5<br>EF6	|No	    |Yes    |Audit<br>Batch Delete<br>Batch Update<br>Cache<br>Future|