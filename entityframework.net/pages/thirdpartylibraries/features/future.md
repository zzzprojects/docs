---
PermaID: 1000016
Name: Future
---

# Future

## Introduction

Future feature allows you to batches multiple LINQ queries to be executed in one SQL Command to perform only a single database round trip.

## Why using Future with Entity Framework?

### Common Scenarios:

 - Reduce the number of database round trip

## Google Related Searches

 - [Entity Framework Future Query](https://www.google.com/search?q=entity+framework+future+query)
 - [Entity Framework Future Value](https://www.google.com/search?q=entity+framework+future+value)

```csharp
// using Z.EntityFramework.Plus; // Don't forget to include this.
var ctx = new EntitiesContext();

// CREATE a pending list of future queries
var futureCountries = db.Countries.Where(x => x.IsActive).Future();
var futureStates = db.States.Where(x => x.IsActive).Future();

// TRIGGER all pending queries in one database round trip
// SELECT * FROM Country WHERE IsActive = true;
// SELECT * FROM State WHERE IsActive = true
var countries = futureCountries.ToList();

// futureStates is already resolved and contains the result
var states = futureStates.ToList();
```

## Supported Libraries

|Library	|Type	|EF Version	|Support	|Doc	|Features|
|:----------|:----------|:----------|:----------|:----------|:----------|
|[Z.EntityFramework.Classic](/ef-classic)	|FREE/PRO	|EF6|< 1 Day	|Yes	| Bulk SaveChanges<br>Bulk Insert<br>Bulk Update<br>Bulk Delete<br>Bulk Merge<br>Batch Delete<br>Batch Update<br>Cache<br>Deferred Query<br>Future|
|[Z.EntityFramework.Plus](/ef-plus)	|FREE	|EF5<br>EF6<br>EF Core|	< 1 Day	|Yes    |Audit<br>Batch Delete<br>Batch Update<br>Cache<br>Deferred Query<br>Filter<br>Future<br>Include Filter<br>Include Optimized|

## Unsuported Libraries

Use this library at your risk!

|Library	|Type	|EF Version	|Support	|Doc	|Features |
|:--------- |:--------- |:--------- |:--------- |:--------- |:--------- |
|[EntityFramework.Extended](/ef-extended)	|FREE	|EF5<br>EF6	|No	    |Yes    |Audit<br>Batch Delete<br>Batch Update<br>Cache<br>Future|