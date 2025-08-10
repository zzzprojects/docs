---
Name: Getting Started
LastMod: 2025-08-09
---

# Overview

## Definition

**Entity Framework Extensions** is a library that dramatically improves EF Core and EF6 performance by using bulk operations.

People using this library often report performance enhancement by 50x and more!

The library is installed through [NuGet](/download). Extension methods are added automatically to your DbContext.

It is easy to use, easy to customize.



```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

// Easy to use
context.BulkSaveChanges();
context.BulkInsert(list);
context.BulkUpdate(list);
context.BulkDelete(list);
context.BulkMerge(list);

// Easy to customize
context.BulkMerge(customers, options => 
	options.ColumnPrimaryKeyExpression = customer => customer.Code);
```

## Installing
Download the [NuGet](/download)NuGet Package</a>

```powershell
PM> NuGet\Install-Package Z.EntityFramework.Extensions.EFCore
```

```bash
> dotnet add package Z.EntityFramework.Extensions.EFCore
```

## Requirements

### EF Core Version

- EF Core 9
- EF Core 8
- EF Core 7
- EF Core 6
- EF Core 5
- EF Core 3
- EF Core 2

### Entity Framework Version

- Entity Framework 6
- Entity Framework 5
- Entity Framework 4

### Database Provider

- SQL Server 2008+
- SQL Azure
- SQL Compact
- Oracle
- MySQL
- MariaDB
- PostgreSQL
- SQLite

## Purpose
Entity Framework is reputed to be very slow when saving multiple entities! The performance issue is mainly due to the **DetectChanges** method and the number of database round-trip.

For example for SQL Server, for every entity you save, a database round-trip must be performed. So, if you need to insert 10000 entities, then 10000 database round-trips will be performed which makes the process **INSANELY** slow.

Entity Framework Extensions, by contrast, only requires a few database round-trips which greatly helps to improve the performance.

## BulkSaveChanges Method

**BulkSaveChanges** method is the upgraded version of **SaveChanges**.

All changes made in the context are persisted in the database but way faster by reducing the number of database round-trips required!

BulkSaveChanges supports everything:

- Association (One to One, One to Many, Many to Many, etc.)
- Complex Type
- Enum
- Inheritance (TPC, TPH, TPT)
- Navigation Property
- Self-Hierarchy
- Etc.

### BulkSaveChanges Examples
```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

context.Customers.AddRange(listToAdd); // add
context.Customers.RemoveRange(listToRemove); // remove
listToModify.ForEach(x => x.DateModified = DateTime.Now); // modify

// Easy to use
context.BulkSaveChanges();

// Easy to customize
context.BulkSaveChanges(bulk => bulk.BatchSize = 100);
```

### Performance Comparisons

| Operations      | 1,000 Entities | 2,000 Entities | 5,000 Entities |
| :-------------- | -------------: | -------------: | -------------: |
| SaveChanges     | 1,000 ms       | 2,000 ms       | 5,000 ms       |
| BulkSaveChanges | 90 ms          | 150 ms         | 350 ms         |

## Bulk Operations Methods

Entity Framework Extensions Bulk operation methods give you additional flexibility by allowing to customize options such as primary key, columns, include child entities and more.

They are also faster than **BulkSaveChanges** since they don't use the ChangeTracker and don't call the **DetectChanges** method.

Bulk Operations Available:

- [BulkInsert](/bulk-insert)
- [BulkUpdate](/bulk-update)
- [BulkDelete](/bulk-delete)
- [BulkMerge](/bulk-merge) (UPSERT operation)
- [BulkSynchronize](/bulk-synchronize)

### Bulk Operations Examples

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

// Easy to use
context.BulkInsert(list);
context.BulkUpdate(list);
context.BulkDelete(list);
context.BulkMerge(list);

// Easy to customize
context.BulkMerge(customers, options => {
	options.ColumnPrimaryKeyExpression = customer => customer.Code; 
});
```

### Performance Comparisons

| Operations      | 1,000 Entities | 2,000 Entities | 5,000 Entities |
| :-------------- | -------------: | -------------: | -------------: |
| SaveChanges     | 1,000 ms       | 2,000 ms       | 5,000 ms       |
| BulkInsert      | 6 ms           | 10 ms          | 15 ms          |
| BulkUpdate      | 50 ms          | 55 ms          | 65 ms          |
| BulkDelete      | 45 ms          | 50 ms          | 60 ms          |
| BulkMerge       | 65 ms          | 80 ms          | 110 ms         |

(Benchmark from EF6)

## Batch Operations Methods

Batch Operations methods allow you to perform **UPDATE** or **DELETE** operations directly in the database using a LINQ Query without loading entities in the context.

Everything is executed on the database side to let you get the best performance available.

Batch Operations Available:
- [DeleteFromQuery](delete-from-query)
- [UpdateFromQuery](update-from-query)

### Batch Operations Examples 
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

(Benchmark from EF6)