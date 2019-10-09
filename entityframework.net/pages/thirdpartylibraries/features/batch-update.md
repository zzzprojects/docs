---
PermaID: 1000026
Name: Batch Update
---

# Batch Update

## Introduction

Batch Update allows you to improve EF performance by updating multiple records in a database from a LINQ Query without loading entities.

## Why Batch Delete?

For **HUGE** performance gains, everything is executed on the database side. To use batch update, you will need to use a third-party library.

## Google Related Searches

 - [Entity Framework Update Without Loading](https://www.google.com/search?q=entity+framework+update+without+loading)
 - [Entity Framework Update Without Retrieving](https://www.google.com/search?q=entity+framework+update+without+retrieving)

## StackOverflow Related Questions

 - [How to Bulk Update records in Entity Framework?](https://stackoverflow.com/questions/44194877/how-to-bulk-update-records-in-entity-framework)
 - [Entity Framework update/insert multiple entities](https://stackoverflow.com/questions/39656794/entity-framework-update-insert-multiple-entities)
 - [Batch update on object list using EntityFramework 6 and Linq](https://stackoverflow.com/questions/30738697/batch-update-on-object-list-using-entityframework-6-and-linq)


```csharp
// using Z.EntityFramework.Plus; // Don't forget to include this.

// UPDATE all users inactive for 2 years
ctx.Users.Where(x => x.LastLoginDate < DateTime.Now.AddYears(-2))
         .Update(x => new User() { IsSoftDeleted = 1 });
```

## Supported Libraries

|Library	|Type	|EF Version	|Support	|Doc	|Features|
|:----------|:----------|:----------|:----------|:----------|:----------|
|[Z.EntityFramework.Classic](/ef-classic)	|FREE/PRO	|EF6|< 1 Day	|Yes	| Bulk SaveChanges<br>Bulk Insert<br>Bulk Update<br>Bulk Delete<br>Bulk Merge<br>Batch Delete<br>Batch Update<br>Cache<br>Deferred Query<br>Future|
|[Z.EntityFramework.Plus](/ef-plus)	|FREE	|EF5<br>EF6<br>EF Core|	< 1 Day	|Yes    |Audit<br>Batch Delete<br>Batch Update<br>Cache<br>Deferred Query<br>Filter<br>Future<br>Include Filter<br>Include Optimized|

## Unsuported Libraries

Use these libraries at your risk!

|Library	|Type	|EF Version	|Support	|Doc	|Features |
|:--------- |:--------- |:--------- |:--------- |:--------- |:--------- |
|[EFUtilities](/ef-utilities)	|FREE	|EF5<br>EF6	|No	    |No |Bulk Insert<br>Batch Delete<br>Batch Update<br>Include Optimized<br>
|[EntityFramework.Extended](/ef-extended)	|FREE	|EF5<br>EF6	|No	    |Yes    |Audit<br>Batch Delete<br>Batch Update<br>Cache<br>Future|