---
Title: InsertFromQuery – Insert Rows Directly from LINQ in Entity Framework
MetaDescription: Insert rows directly from LINQ with Entity Framework InsertFromQuery. Run pure SQL INSERTs without loading entities, tracking changes, or SaveChanges.
LastMod: 2026-05-05
---

# What is InsertFromQuery

`InsertFromQuery` is a **FREE** method supported by both **EF Core** and **EF6**. It lets you insert database rows **directly in SQL**:

* Without **reading data** or loading entities into memory
* Without using the **Change Tracker**
* Without calling `SaveChanges`

Think of `InsertFromQuery` as a **SQL INSERT statement generated from LINQ**.

`InsertFromQuery` is a set-based operation executed entirely in SQL.

You should use `InsertFromQuery` when you want to **copy rows or values from one table to another (or the same table)**. If you want to insert entities, you should instead use [BulkInsert](/bulk-insert).

## InsertFromQuery Example

### Insert all rows into a backup table

To insert all rows into another table, you need to:

* Specify the **table name**
* Choose the **properties to insert**

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

// INSERT all customers into the "bck_Customer" table
await context.Customers
    .InsertFromQueryAsync(
        "bck_Customer",
        x => new { x.CustomerID, x.Name, x.Email }
    );
```

[Online Example](https://dotnetfiddle.net/jXj29K)

The destination table doesn’t have to be part of your model. You can choose **any table name** and select **which columns (through properties)** you want to insert.

### Insert rows into another mapped entity type

You can insert into another mapped entity type by providing the **type** instead of a **table name**.

Use this approach when the destination entity is **already mapped in your model**. EF will automatically use the configured table and column mappings.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

// INSERT all customers into the "bck_Customer" table
await context.Customers
    .InsertFromQueryAsync(
        typeof(BackupCustomer),
        x => new { x.CustomerID, x.Name, x.Email }
    );
```

[Online Example](https://dotnetfiddle.net/LmnwII)

### Insert rows using a "Where" filter

Like a standard LINQ query, you can filter the rows you want to insert using the `Where` method.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

// INSERT all customers that are active but haven't logged in for more than two years into the "bck_Customer" table
var date = DateTime.Now.AddYears(-2);

await context.Customers
    .Where(x => x.IsActive && x.LastLogin < date)
    .InsertFromQueryAsync(
        "bck_Customer",
        x => new { x.CustomerID, x.Name, x.Email }
    );
```

[Online Example](https://dotnetfiddle.net/UvIOBU)

### Insert rows using a "WhereBulkContains" filter

You can also use methods like [WhereBulkContains](/where-bulk-contains) to filter rows to insert, especially when working with large lists of values.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

// INSERT only specific customers into the "bck_Customer" table
var ids = new List<int>() { 1, 2 };

await context.Customers
    .WhereBulkContains(ids)
    .InsertFromQueryAsync(
        "bck_Customer",
        x => new { x.CustomerID, x.Name, x.Email }
    );
```

[Online Example](https://dotnetfiddle.net/5IIhzH)

### Insert rows using options

You can also use the `InsertFromQuery` method by passing options. This gives you **full control** over how the insert is executed.

Use this overload when the standard method is not enough and you need to customize the behavior.

In this example, we:

* Specify the **table name**
* Define the **select expression**
* Add an **event executed before the command runs** to log the SQL command

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

await context.Customers.InsertFromQueryAsync(options =>
{
    options.TableName = "bck_Customer";
    options.SelectExpression = x => new { x.CustomerID, x.Name, x.Email };

    // Log the generated SQL before execution
    options.Executing = command => Console.WriteLine(command.CommandText);
});
```

[Online Example](https://dotnetfiddle.net/CGSMyF)

## InsertFromQuery Options

You can pass the destination table name and the select expression as parameters, but you can also use **options** to configure them.

Using options gives you **more flexibility** and allows you to customize how the insert is executed.

We support the following options:

* **DatabaseName:** To specify the database name
* **SchemaName:** To specify the schema name
* **TableName:** To specify the table name
* **SelectExpression:** To specify the select expression (properties to insert)
* **TableType:** To specify the destination table type
* **Executing:** To raise an event just before the command is executed

You can specify **one or more options**, depending on your requirements.

### DatabaseName / SchemaName / TableName

The destination table can have **any name** and can also be located in **another database**.

Use these options when you need to fully qualify the destination table.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

context.Customers.InsertFromQuery(options =>
{
    options.DatabaseName = "MyDatabaseName";
    options.SchemaName = "MySchemaName";
    options.TableName = "MyTableName";
	
    // Select only the properties you want to insert
    options.SelectExpression = x => new
    {
        x.CustomerID,
        x.Name,
        x.Email
    };
});
```

### TableType

Instead of providing a fully qualified destination table name, you can use `TableType` when the destination entity type is **mapped in your `DbContext`**.

EF will automatically use the configured table and column mappings.

By default, properties are matched automatically between the source and the target. The property names must match.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

context.Customers.InsertFromQuery(options =>
{
    options.TableType = typeof(BackupCustomer);
	
    // Select only the properties you want to insert
    options.SelectExpression = x => new
    {
        x.CustomerID,
        x.Name,
        x.Email
    };
});
```

### SelectExpression

Use `SelectExpression` to specify **which columns to insert** when using options.

This option lets you choose exactly **which properties from the source (e.g., `Customers`)** are included in the insert.

By default, properties are matched automatically with the destination table. 

When using the options overload, you **must specify** a `SelectExpression` to define the columns being inserted.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

context.Customers.InsertFromQuery(options =>
{
    options.TableName = "bck_Customer";

    // Select only the properties you want to insert
    options.SelectExpression = x => new
    {
        x.CustomerID,
        x.Name,
        x.Email
    };
});
```

### Executing

Use `Executing` to raise an event **just before the command is executed**.

This option is useful to **inspect, log, or even modify the generated SQL** before it runs.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

context.Customers.InsertFromQuery(options =>
{
    // Log the generated SQL before execution
    options.Executing = command =>
    {
        Console.WriteLine(command.CommandText);

        // You can also modify the command text if needed
        // command.CommandText = command.CommandText + " -- custom comment";
    };
	
    options.TableName = "MyTableName";
	
    // Select only the properties you want to insert
    options.SelectExpression = x => new
    {
        x.CustomerID,
        x.Name,
        x.Email
    };
});
```

## FAQ

### How to increase the CommandTimeout?

If a timeout occurs, you can increase the command timeout by setting it on the database context **before calling** the `InsertFromQuery` method.

This is useful when inserting a large number of rows or when the query takes more time to execute.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

context.Database.SetCommandTimeout(TimeSpan.FromSeconds(60));

context.Customers
    .InsertFromQuery(
        "bck_Customer",
        x => new { x.CustomerID, x.Name, x.Email }
    );
```

### Is the InsertFromQuery feature FREE?

Yes, you don’t need to buy a commercial license to use it.

`InsertFromQuery` is **completely free**, including for **commercial use**.

You can use it in both personal and professional projects without any restriction.

### Is there an EF Core equivalent?

No, `InsertFromQuery` is a **unique feature** of Entity Framework Extensions.

There is no built-in equivalent in EF Core to insert data directly from a query **without loading entities into memory**.

In EF Core, you would typically need to:

* Load entities into memory and call `SaveChanges`, or
* Write raw SQL manually

### Why is InsertFromQuery faster than SaveChanges, BulkSaveChanges, and BulkInsert?

`InsertFromQuery` executes a statement directly in SQL, such as `INSERT INTO ... SELECT ... FROM ...`.

This means everything is done **on the database side**, without loading data into memory.

Other operations usually:

* Load data into memory
* Track entities
* Then send insert commands

Because `InsertFromQuery` skips all those steps, it is usually **much faster and more efficient**, especially for large operations.

## Summary

`InsertFromQuery` lets you insert rows **directly from LINQ**, without loading entities or using change tracking.

It runs entirely in SQL, scales very well, and keeps memory usage low.

This feature is **unique to Entity Framework Extensions** and is **FREE to use**.

If you need to:

* **Insert rows from a query**: Use [InsertFromQuery](/insert-from-query)
* **Update rows from a query**: Use [UpdateFromQuery](/update-from-query)
* **Delete rows from a query**: Use [DeleteFromQuery](/delete-from-query)

Together, these methods provide a complete set of **set-based operations** executed directly at the database level.
