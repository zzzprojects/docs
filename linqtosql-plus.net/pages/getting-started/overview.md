# Overview

## Definition

**LinqToSql Plus** is a library that dramatically improves LinqToSql performances by using bulk and batch operations.

People using this library often report performance enhancement by 50x times and more!

The library is installed through <a href="/download">NuGet</a>. Extension methods are added automatically to your DbContext.

It easy to use and easy to customize.



```csharp
// Easy to use
context.BulkInsert(list);
context.BulkUpdate(list);
context.BulkDelete(list);
context.BulkMerge(list);

// Easy to customize
context.BulkMerge(customers, options => 
	options.ColumnPrimaryKeyExpression = customer => customer.Code);
```

## Installing
Download the <a href="/download">NuGet Package</a>

## Requirements

### LinqToSql
- System.Data.Linq

### Database Provider

- SQL Server 2008+
- SQL Azure

## Bulk Operations Methods

Bulk operation methods give you additional flexibility by allowing to customize options such as primary key, columns and more.

Bulk Operations Available:

- [BulkInsert](/bulk-insert)
- [BulkUpdate](/bulk-update)
- [BulkDelete](/bulk-delete)
- [BulkMerge](/bulk-merge) (UPSERT operation)
- [BulkSynchronize](/bulk-synchronize)

### Bulk Operations Examples
```csharp
// Easy to use
context.BulkInsert(list);
context.BulkUpdate(list);
context.BulkDelete(list);
context.BulkMerge(list);

// Easy to customize
context.BulkMerge(customers, options => 
	options.ColumnPrimaryKeyExpression = customer => customer.Code; });
```

### Performance Comparisons

| Operations      | 1,000 Entities | 2,000 Entities | 5,000 Entities |
| :-------------- | -------------: | -------------: | -------------: |
| SubmitChanges   | 1,000 ms       | 2,000 ms       | 5,000 ms       |
| BulkInsert      | 6 ms           | 10 ms          | 15 ms          |
| BulkUpdate      | 50 ms          | 55 ms          | 65 ms          |
| BulkDelete      | 45 ms          | 50 ms          | 60 ms          |
| BulkMerge       | 65 ms          | 80 ms          | 110 ms         |

## Batch Operations Methods

Batch Operations method allow to perform **UPDATE** or **DELETE** operation directly in the database using a LINQ Query without loading entities in the context.

Everything is executed on the database side to let you get the best performance available.

Batch Operations Available:
- [DeleteFromQuery](delete-from-query)
- [UpdateFromQuery](update-from-query)

### Batch Operations Examples
```csharp
// DELETE all customers that are inactive for more than two years
context.Customers
    .Where(x => x.LastLogin < DateTime.Now.AddYears(-2))
    .DeleteFromQuery();
 
// UPDATE all customers that are inactive for more than two years
context.Customers (Coming Soon)
    .Where(x => x.IsActive && x.LastLogin < DateTime.Now.AddYears(-2))
    .UpdateFromQuery(x => new Customer {IsActive = false});
```

### Performance Comparisons

| Operations      | 1,000 Entities | 2,000 Entities | 5,000 Entities |
| :-------------- | -------------: | -------------: | -------------: |
| SubmitChanges   | 1,000 ms       | 2,000 ms       | 5,000 ms       |
| DeleteFromQuery | 1 ms           | 1 ms           | 1 ms           |
| UpdateFromQuery | 1 ms           | 1 ms           | 1 ms           |
