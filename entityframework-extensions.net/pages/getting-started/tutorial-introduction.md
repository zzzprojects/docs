---
Name: Getting Started Introduction
LastMod: 2025-06-26
---

# Introduction

## Introduction
Entity Framework Extensions allow you to dramatically improve the performance of your save operations.

It's easy to use, and easy to customize.

## Bulk SaveChanges
The BulkSaveChanges works like SaveChanges but way faster.

BulkSaveChanges uses Bulk Operations to save all entities in the Change Tracker efficiently instead of performing a database round-trip for every entity like SaveChanges does.

BulkSaveChanges supports everything:

- Complex Types
- Inheritance (TPC, TPH, TPT)
- Relationship (One to One, One to Many, Many to Many)

### Example

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

var ctx = new EntitiesContext();

ctx.Customers.AddRange(listToAdd); // add
ctx.Customers.RemoveRange(listToRemove); // remove
listToModify.ForEach(x => x.DateModified = DateTime.Now); // modify

// Easy to use
ctx.BulkSaveChanges();

// Easy to customize
ctx.BulkSaveChanges(bulk => bulk.BatchSize = 100);
```
### Performance Comparisons

| Operations      | 1,000 Entities | 2,000 Entities | 5,000 Entities |
| :-------------- | -------------: | -------------: | -------------: |
| SaveChanges     | 1,000 ms       | 2,000 ms       | 5,000 ms       |
| BulkSaveChanges | 90 ms          | 150 ms         | 350 ms         |

## Bulk Operations

Bulk Operations methods provide you with some flexibility by allowing some customization and performance enhancement.

All common methods are supported:

- BulkInsert
- BulkUpdate
- BulkDelete
- BulkMerge (UPSERT operation)
- BulkSynchronize

### Example


```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

var ctx = new EntitiesContext();

// Easy to use
ctx.BulkInsert(list);
ctx.BulkUpdate(list);
ctx.BulkDelete(list);
ctx.BulkMerge(list);

// Easy to customize
ctx.BulkMerge(customers, 
   bulk => bulk.ColumnPrimaryKeyExpression = customer => customer.Code);
```

### Performance Comparisons

| Operations      | 1,000 Entities | 2,000 Entities | 5,000 Entities |
| :-------------- | -------------: | -------------: | -------------: |
| SaveChanges     | 1,000 ms       | 2,000 ms       | 5,000 ms       |
| BulkInsert      | 6 ms           | 10 ms          | 15 ms          |
| BulkUpdate      | 50 ms          | 55 ms          | 65 ms          |
| BulkDelete      | 45 ms          | 50 ms          | 60 ms          |
| BulkMerge       | 65 ms          | 80 ms          | 110 ms         |

## FromQuery Operations

The FromQuery method allows you to execute UPDATE or DELETE statements without loading entities in the context.

### Example


```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

// DELETE all customers that are inactive for more than two years
context.Customers
    .Where(x => x.LastLogin < DateTime.Now.AddYears(-2))
    .DeleteFromQuery();
 
// UPDATE all customers that are inactive for more than two years
context.Customers
    .Where(x => x.IsActive && x.LastLogin < DateTime.Now.AddYears(-2))
    .UpdateFromQuery(x => new Customer {IsActive = false});
```

### Performance Comparisons

| Operations      | 1,000 Entities | 2,000 Entities | 5,000 Entities |
| :-------------- | -------------: | -------------: | -------------: |
| SaveChanges     | 1,000 ms       | 2,000 ms       | 5,000 ms       |
| DeleteFromQuery | 1 ms           | 1 ms           | 1 ms           |
| UpdateFromQuery | 1 ms           | 1 ms           | 1 ms           |
