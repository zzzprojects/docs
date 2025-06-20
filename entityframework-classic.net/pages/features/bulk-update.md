---
Name: Bulk Update
CanonicalLink: https://entityframework-extensions.net/bulk-update
---

# Bulk Update

## Description
The **EF Bulk Update** feature lets you update thousands of entities in your database efficiently.

This feature is provided by the library [EF Extensions](https://entityframework-extensions.net/bulk-update) _(Included with EF Classic)_. EF Extensions is used by over 2000 customers all over the world and supports all Entity Framework versions (EF4, EF5, EF6, EF Core, EF Classic).

```csharp
// Easy to use
context.BulkUpdate(customers);

// Easy to customize
context.BulkUpdate(customers, options => options.ColumnPrimaryKeyExpression = customer => customer.Code);
```
Try it: [NET Core](https://dotnetfiddle.net/EIjAFh) | [NET Framework](https://dotnetfiddle.net/WYxuyf)

## Performance Comparison

| Operations      | 1,000 Entities | 2,000 Entities | 5,000 Entities |
| :-------------- | -------------: | -------------: | -------------: |
| SaveChanges     | 1,200 ms       | 2,400 ms       | 6,000 ms       |
| BulkUpdate      | 80 ms          | 110 ms         | 170 ms         |

Try it: [NET Core](https://dotnetfiddle.net/8BnXFR) | [NET Framework](https://dotnetfiddle.net/RAuYhO)

> HINT: Performance may differ from one database to another. A lot of factors might affect the benchmark time such as index, column type, latency, throttling, etc.

### Why BulkUpdate is faster than SaveChanges?
Updating thousands of entities for a file import is a typical scenario.

The `SaveChanges` method makes it quite impossible to handle this kind of situation due to the number of database round-trips required. The `SaveChanges` performs one database round-trip for every entity to update. So, if you need to update 10,000 entities, 10,000 database round-trips will be performed which is **INSANELY** slow.

The `BulkUpdate` on the other hand requires the minimum database round-trips possible. For example, under the hood of SQL Server, a `SqlBulkCopy` is performed first in a temporary table, then an `UPDATE` from the temporary table to the destination table is performed which is the most effective tactic available.

## Real-Life Scenarios

## Bulk Update with custom key
You need to update a list of `Customer` but you don't have the IDs, you only have the unique customer codes. The [ColumnPrimaryKeyExpression](https://entityframework-extensions.net/column#column-primary-key) lets you choose the key to use.

```csharp
context.BulkUpdate(customers, options => options.ColumnPrimaryKeyExpression = customer => customer.Code);
```
Try it: [NET Core](https://dotnetfiddle.net/8ZuEW4) | [NET Framework](https://dotnetfiddle.net/dMGZcV)

## Bulk Update specific columns
You need to update a list of `Customer` but only update some specific columns such as FirstName and LastName. The [ColumnInputExpression](https://entityframework-extensions.net/column#column-input) option lets you choose the columns to update.

```csharp
context.BulkUpdate(customers, options => { 
    options.ColumnInputExpression = customer => new { customer.FirstName, customer.LastName };
    options.ColumnPrimaryKeyExpression = customer => customer.Code;
});
```
Try it: [NET Core](https://dotnetfiddle.net/zUu8m8) | [NET Framework](https://dotnetfiddle.net/WBgGqx)

## Bulk Update invoice and related invoice items (Include Graph)
You need to update a list of `Invoice` and include related `InvoiceItem`. By default, the `BulkUpdate` doesn't include the graph but you can enable it with the [IncludeGraph](https://entityframework-extensions.net/include-graph) option.

```csharp
context.BulkUpdate(invoices, options => options.IncludeGraph = true);
```
Try it: [NET Core](https://dotnetfiddle.net/ovvcp5) | [NET Framework](https://dotnetfiddle.net/ljXay5)

## Documentation

### BulkUpdate

###### Methods

| Name | Description | Example |
| :--- | :---------- | :------ |
| `BulkUpdate<T>(items)` | Bulk update entities in your database. | [NET Core](https://dotnetfiddle.net/MBZH1g) / [NET Framework](https://dotnetfiddle.net/nhDfC3)|
| `BulkUpdate<T>(items, options)` | Bulk update entities in your database.  | [NET Core](https://dotnetfiddle.net/cnaUpK) / [NET Framework](https://dotnetfiddle.net/vBTSn1) |
| `BulkUpdateAsync<T>(items)` | Bulk update entities asynchronously in your database. | [NET Core](https://dotnetfiddle.net/WUKxR9) / [NET Framework](https://dotnetfiddle.net/mztFjj) |
| `BulkUpdateAsync<T>(items, cancellationToken)` | Bulk update entities asynchronously in your database. | [NET Core](https://dotnetfiddle.net/AljSYK) / [NET Framework](https://dotnetfiddle.net/Fjkzm2) |
| `BulkUpdateAsync<T>(items, options, cancellationToken)` | Bulk update entities asynchronously in your database. | [NET Core](https://dotnetfiddle.net/iu37bl) / [NET Framework](https://dotnetfiddle.net/U84QYD) |

## Learn more

More documentation can be found here: [EF Extensions - Bulk Update](https://entityframework-extensions.net/bulk-update)
