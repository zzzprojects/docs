---
Title: Additional EF Core & EF6 Features Beyond Bulk Operations
MetaDescription: Discover EF Extensions features beyond bulk operations: BulkSaveChanges, BatchSaveChanges, from-query methods, and advanced Where methods.
LastMod: 2025-12-20
---

# Entity Framework Additional Features

We have seen in the [previous getting started](/tutorial-bulk-operations) article that **Entity Framework Extensions (EF Extensions)** extends EF Core and EF6 with bulk operations.

Bulk operations are usually the first reason people start using EF Extensions.

However, as you use Entity Framework more, you quickly discover that many scenarios require **different ways to read, save, or process data**, depending on your needs.

That’s where the additional features of EF Extensions come in.

In this section, we will explore the following features that help you **work more efficiently**, **handle advanced scenarios**, and **keep your code simple and fast**:

* [BulkSaveChanges Method](#bulksavechanges-method)
* [BatchSaveChanges Method](#batchsavechanges-method)
* [Batch Operations Methods](#batch-operations-methods)
  * InsertFromQuery
  * UpdateFromQuery
  * DeleteFromQuery
* [Where Methods](#where-methods)
  * BulkRead
  * WhereBulkContains
  * WhereNotBulkContains
  * WhereBulkContainsFilterList
  * WhereBulkNotContainsFilterList

## BulkSaveChanges Method

EF Extensions allows you to improve performance in EF Core and EF6 by replacing the `SaveChanges` method with the `BulkSaveChanges` method.

As the name indicates, `BulkSaveChanges` saves data more efficiently by using bulk operations under the hood.

To use this method, you simply need to call `BulkSaveChanges` instead of `SaveChanges`:

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

// Easy to use
context.BulkSaveChanges();

// Easy to customize
context.BulkSaveChanges(options => options.BatchSize = 100);
```

Learn more about [BulkSaveChanges](/bulk-savechanges).

## BatchSaveChanges Method

The `BatchSaveChanges` method is only available for EF6.

This method allows you to batch multiple save commands into fewer database commands, which increases the overall saving performance.

In fact, the performance improvement was so significant that EF Core later adopted a similar approach for multiple providers such as PostgreSQL, MySQL, and MariaDB.

To use this method, you simply need to call `BatchSaveChanges` instead of `SaveChanges`:

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

var affectedRows = context.BatchSaveChanges();
```

The performance improvement is often around **500% faster**:

| Operations       | 50 Entities | 200 Entities | 500 Entities |
| :--------------- | ----------: | -----------: | -----------: |
| SaveChanges      |       65 ms |       250 ms |       600 ms |
| BatchSaveChanges |       25 ms |        50 ms |       125 ms |

`BatchSaveChanges` provides the best performance when you have a few hundreds of entities to save, but it does not scale as well as `BulkSaveChanges`.

For thousands of entities, `BulkSaveChanges` is the recommended approach.

Learn more about [BatchSaveChanges](/batch-savechanges).

## Batch Operations Methods

The batch operations methods, also called **“from query” methods**, allow you to perform `INSERT`, `UPDATE`, or `DELETE` operations **directly in the database** by using a LINQ query, without loading entities into the context.

Because these operations are executed directly in SQL, they **bypass the Change Tracker** and work on **sets of rows at once**, which makes them extremely fast and memory-efficient.

These methods are ideal for **data maintenance**, **cleanup tasks**, **status updates**, and **one-time or recurring operations** where loading entities is unnecessary.

There are **3 methods** that support both EF Core and EF6:

* [InsertFromQuery](/insert-from-query)
* [UpdateFromQuery](/update-from-query)
* [DeleteFromQuery](/delete-from-query)

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

var dateToDeactivate = DateTime.Now.AddYears(-2);

// INSERT inactive customers into a backup table
context.Customers
    .Where(x => !x.IsActive && !x.Invoices.Any())
    .InsertFromQuery("bck_Customers",
        x => new { x.CustomerID, x.Code, x.Name, x.Email, x.LastLogin });

// UPDATE active customers who haven’t logged in recently
context.Customers
    .Where(x => x.IsActive && x.LastLogin < dateToDeactivate)
    .UpdateFromQuery(x => new Customer { IsActive = false });

// DELETE inactive customers
context.Customers
    .Where(x => !x.IsActive && !x.Invoices.Any())
    .DeleteFromQuery();
```

For people using **EF Core 7+**, you can alternatively use the built-in methods:

* [ExecuteUpdate](https://www.learnentityframeworkcore.com/dbset/execute-update)
* [ExecuteDelete](https://www.learnentityframeworkcore.com/dbset/execute-delete)
* (No built-in method is available to replace `InsertFromQuery`)

## Where Methods

Where methods are **five methods** added to both EF Core and EF6 to improve **how data is read and filtered**.

These methods are designed to solve scenarios where the standard LINQ `Where` and `Contains` methods **don’t scale well** or simply **cannot handle complex inputs**.

The available methods are:

* [BulkRead](/bulk-read): Retrieve entities using a filtered query
* [WhereBulkContains](/where-bulk-contains): Filter the query to include matching items
* [WhereNotBulkContains](/where-bulk-not-contains): Filter the query to exclude matching items
* [WhereBulkContainsFilterList](/where-bulk-contains-filter-list): Return entities from the list that exist in the database
* [WhereBulkNotContainsFilterList](/where-bulk-not-contains-filter-list): Return entities from the list that don’t exist in the database

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

// --- Retrieval Operations ---
context.Customers.BulkRead(deserializedCustomers); // Retrieve entities using a filtered query
context.Customers.WhereBulkContains(deserializedCustomers).ToList(); // Include matching items
context.Customers.WhereBulkNotContains(deserializedCustomers).ToList(); // Exclude matching items
context.Customers.WhereBulkContainsFilterList(deserializedCustomers).ToList(); // Return list items that exist in the database
context.Customers.WhereBulkNotContainsFilterList(deserializedCustomers).ToList(); // Return list items that don't exist in the database
```

These methods are especially useful when working with **large lists** or **external data sources**, such as deserialized files, APIs, or in-memory collections.

Their main advantages are:

* Can be used with **millions of entities**
* Support **custom keys** and **composite keys**
* Support **all kinds of lists**:
  * Basic types such as `List<int>` and `List<Guid>`
  * Entity types such as `List<Customer>`
  * Anonymous types
  * Expando object lists

All of these scenarios are **impossible** to handle with the traditional LINQ `Where` and `Contains` methods.

The main documentation entry point for these features is the
[WhereBulkContains](/where-bulk-contains) method.

## Summary & Next Steps

Entity Framework Extensions goes far beyond basic bulk operations.

The additional features covered in this article help you handle advanced scenarios efficiently, without loading unnecessary data or writing raw SQL.

As a rule of thumb:

* Use **BulkSaveChanges** for an easy performance upgrade with minimal code changes
* Use **BatchSaveChanges** when working with EF6 and saving a few hundreds of entities
* Use **from query methods** for maintenance tasks, cleanup jobs, or large set-based operations
* Use **Where methods** when LINQ `Where` and `Contains` no longer scale

From here, you can explore the individual documentation pages linked above to see practical examples, advanced options, and best practices.