---
PermaID: 1000017
Name: Filter
---

# Filter

## Introduction

Filter allows you to filter data from the DbContext with predefined filters.

## Why Filters?

### Common Scenarios:

 - Default Ordering
 - Logical Data Partitioning
 - Multi-Tenancy
 - Soft Deleted
 - Security Access

## Google Related Searches

 - [Entity Framework Dynamic Filter](https://www.google.com/search?q=entity+framework+dynamic+filter)
 - [Entity Framework Global Filter](https://www.google.com/search?q=entity+framework+global+filter)
 - [Entity Framework Soft Delete Filter](https://www.google.com/search?q=entity+framework+soft+delete+filter)

## StackOverflow Related Questions

 - [Filter all tables on Context Level](https://stackoverflow.com/questions/39934051/filter-all-tables-on-context-level)


```csharp
// using Z.EntityFramework.Plus; // Don't forget to include this.
var ctx = new EntitiesContext();

ctx.Filter<Post>(q => q.Where(x => !x.IsSoftDeleted));

// SELECT * FROM Post WHERE IsSoftDeleted = false
var list = ctx.Posts.ToList();
```

## Supported Libraries


|Library	|Type	|EF Version	|Support	|Doc	|Features|
|:----------|:----------|:----------|:----------|:----------|:----------|
|[Z.EntityFramework.Plus](/ef-plus)	|FREE	|EF5<br>EF6<br>EF Core|	< 1 Day	|Yes    |Audit<br>Batch Delete<br>Batch Update<br>Cache<br>Deferred Query<br>Filter<br>Future<br>Include Filter<br>Include Optimized|
|[EntityFramework.DynamicFilters](/ef-dynamic-filters)	|FREE	|EF6	|< 2 Days	|Yes    |Filter |

## Unsuported Libraries

Use this library at your risk!

|Library	|Type	|EF Version	|Support	|Doc	|Features |
|:--------- |:--------- |:--------- |:--------- |:--------- |:--------- |
|[EntityFramework.Filters](/ef-filters)	|FREE	|EF6	|No	    |Yes    |Filter |