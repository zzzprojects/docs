---
Title: Entity Framework InsertFromQuery – Insert Rows Directly from LINQ
MetaDescription: Insert rows directly from LINQ with Entity Framework InsertFromQuery. Run pure SQL INSERTs without loading entities, tracking changes, or SaveChanges.
LastMod: 2025-12-24
---

# Entity Framework InsertFromQuery

## What is InsertFromQuery

`InsertFromQuery` is a **FREE** method supported by both **EF Core** and **EF6** (see also [UpdateFromQuery](/update-from-query) and [DeleteFromQuery](/delete-from-query)). It lets you insert database rows **directly in SQL**:

* Without **reading data** or loading entities into memory
* Without using the **Change Tracker**
* Without calling `SaveChanges`

Think of `InsertFromQuery` as a **SQL INSERT generated from LINQ**.

You should use `InsertFromQuery` when you want to **copy rows or values from one database table to another (or the same one)**. If you want to insert entities, you should instead use [BulkInsert](/bulk-insert).

`InsertFromQuery` is available in both synchronous and asynchronous versions:

* `InsertFromQuery`
* `InsertFromQueryAsync`

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

// INSERT all customers that are inactive for more than two years into a backup table
var date = DateTime.Now.AddYears(-2);

context.Customers
    .Where(x => x.IsActive && x.LastLogin < date)
    .InsertFromQuery(
        "bck_Customer",
        x => new { x.CustomerID, x.Name, x.Email }
    );
```

[Online Example](https://dotnetfiddle.net/UvIOBU)

The destination table doesn’t have to be part of your model. You can choose **any table name** and **which columns (through properties)** you want to insert.

## InsertFromQuery Options

You can pass the destination table name and the select expression as parameters, but you can also use **options** to configure them.

We support the following options:

* **DatabaseName:** To specify the database name
* **SchemaName:** To specify the schema name
* **TableName:** To specify the table name
* **SelectExpression:** To specify the select expression (properties to insert)
* **TableType:** To specify the destination table type
* **Executing:** To raise an event just before the command is executed

You can specify **one or more options**, depending on your requirements.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

// INSERT all customers that are inactive for more than two years into a backup table
var date = DateTime.Now.AddYears(-2);

context.EntitySimples.InsertFromQuery(options =>
{
    options.TableName = "bck_Customer";
    options.SelectExpression = x => new { x.ColumnInt };
    options.Executing = command => Console.WriteLine(command.CommandText);
});
```

## Summary & Next Steps

`InsertFromQuery` lets you insert rows **directly from LINQ**, without loading entities or using change tracking.

It runs entirely in SQL, scales very well, and keeps memory usage low.

This feature is **unique to Entity Framework Extensions** and is **FREE to use**.

If you need to:

* **Insert rows from a query**: Use [InsertFromQuery](/insert-from-query)
* **Update rows from a query**: Use [UpdateFromQuery](/update-from-query)
* **Delete rows from a query**: Use [DeleteFromQuery](/delete-from-query)

Together, these methods provide a complete set of **set-based operations** executed directly at the database level.
