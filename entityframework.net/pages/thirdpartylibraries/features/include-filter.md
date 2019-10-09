---
PermaID: 1000015
Name: Include Filter
---

# Include Filter

## Introduction

IncludeFilter allows you to add a method similar to EF Include but with a predicate to filter related entities.

## Why IncludeFilter?

### Common Scenarios:

 - Logical Data Partitioning
 - Multi-Tenancy
 - Soft Deleted
 - Security Access

## Google Related Searches

 - [Entity Framework Include Filter Child Collection](https://www.google.com/search?q=entity+framework+include+filter+child+collection)
 - [Entity Framework Include Where Clause](https://www.google.com/search?q=entity+framework+include+where+clause)
 - [Entity Framework Filter Related Entities](https://www.google.com/search?q=entity+framework+filter+related+entities)

## StackOverflow Related Questions

 - [How to filter "Include" entities in entity framework?](https://stackoverflow.com/questions/39636952/how-to-filter-include-entities-in-entity-framework)
 - [How to filter child collections Entity Framework](https://stackoverflow.com/questions/39046597/how-to-filter-child-collections-entity-framework)


```csharp
// using Z.EntityFramework.Plus; // Don't forget to include this.
var ctx = new EntitiesContext();

// LOAD orders and the first 10 active related entities.
var list = ctx.Orders.IncludeFilter(x => x.Items.Where(y => !y.IsSoftDeleted)
                                               .OrderBy(y => y.Date)
                                               .Take(10))
                                               .ToList();
```

## Supported Libraries

|Library	|Type	|EF Version	|Support	|Doc	|Features|
|:----------|:----------|:----------|:----------|:----------|:----------|
|[Z.EntityFramework.Plus](/ef-plus)	|FREE	|EF5<br>EF6<br>EF Core|	< 1 Day	|Yes    |Audit<br>Batch Delete<br>Batch Update<br>Cache<br>Deferred Query<br>Filter<br>Future<br>Include Filter<br>Include Optimized|