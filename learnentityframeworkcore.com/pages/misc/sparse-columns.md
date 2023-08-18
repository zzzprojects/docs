---
title: Sparse Columns in Entity Framework Core
description: Support for SQL Server sparse columns
canonical: /misc/sparse-columns
status: Published
lastmod: 2023-02-16
---

# EF Core SQL Server Sparse Columns

In SQL Server, a sparse column is a column that is optimized for storing null values. Sparse columns allow you to save storage space by not storing any data for columns that have a null value.

When you create a sparse column, you need to specify the `SPARSE` attribute. For example, the following SQL statement creates a table with a sparse column:

```csharp
CREATE TABLE Books (
   Price INT SPARSE,
   Name VARCHAR(50)
);
```

In this example, the `Price` is a sparse column, while the `Name` is a regular column. When a row is inserted into the `Books` table and the `Price` value is null, SQL Server will not allocate any space to store the null value.

Sparse columns can be useful in situations where you have a large number of columns in a table, and many of those columns have null values. By using sparse columns, you can reduce the amount of storage space needed for the table, which can improve performance and reduce storage costs.

## Support for Sparse Columns in EF Core

When working with a SQL Server database that contains sparse columns, EF Core will create corresponding entity properties that are nullable. For example, if you have a table named `Products` with a sparse column named `Price`, and a non-sparse column named `Name`, the EF Core code to represent this table might look something like this:

```csharp
public class Product
{
    public int Id { get; set; }
    public string Name { get; set; }
    public decimal? Price { get; set; }
}
```

The `Price` property is nullable (`decimal?`) to account for the fact that the sparse column may contain null values. When EF Core retrieves data from the database, it will populate the `Price` property with null if the corresponding database value is null.


To configure a sparse column in EF Core, you can use the `IsSparse` in `OnModelCreating`.

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder
        .Entity<Product>()
        .Property(e => e.Price)
        .IsSparse();
}
```

The `IsSparse` method allows you to specify that a `Price` column should be treated as a sparse column in the database.

```csharp
CREATE TABLE [dbo].[Products] (
    [Id]    INT                    IDENTITY (1, 1) NOT NULL,
    [Name]  NVARCHAR (MAX)         NOT NULL,
    [Price] DECIMAL (18, 2) SPARSE NULL,
    CONSTRAINT [PK_Products] PRIMARY KEY CLUSTERED ([Id] ASC)
);
```

## Limitations

EF Core supports working with SQL Server sparse columns by representing them as nullable entity properties. However, there are some limitations and provider-specific considerations to be aware of.

 - EF Core does not support creating unique indexes on sparse columns, and you cannot include sparse columns in a clustered index. 
 - Additionally, not all database providers support sparse columns - for example, the SQLite provider does not support them.
