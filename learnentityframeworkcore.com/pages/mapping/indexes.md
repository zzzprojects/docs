---
title: Index in EF Core
description: Learn how to configure indexes in EF Core using Data Annotations and Fluent API, including composite, unique, filtered, and descending indexes.
canonical: /mapping/index
status: Published
lastmod: 2026-11-23
---

# Index in EF Core

An index is a database structure that helps the database find rows faster without scanning the entire table. In EF Core, you can configure indexes using Data Annotations or Fluent API.

The examples below show alternative configurations of a `Product` entity; each code block stands on its own. For more configuration examples, see our [Index Attribute](/configuration/data-annotation-attributes/index-attribute) and [HasIndex Method](/configuration/fluent-api/hasindex-method) guides.

**Data Annotations**

```csharp
[Index(nameof(Name))]
public class Product
{
    public int ProductId { get; set; }
    public string Name { get; set; } = null!;
}
```

**Fluent API**

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Product>()
        .HasIndex(p => p.Name);
}
```

Both approaches configure an index on `Name`. The database can use it to find matching products more efficiently.

## Composite Index

A composite index covers multiple columns. It is useful when queries frequently filter or sort by the same combination of properties.

**Data Annotations**

```csharp
[Index(nameof(Code), nameof(Category))]
public class Product
{
    public int ProductId { get; set; }
    public string Code { get; set; } = null!;
    public string Category { get; set; } = null!;
}
```

**Fluent API**

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Product>()
        .HasIndex(p => new { p.Code, p.Category });
}
```

The index contains `Code` first, followed by `Category`. This order matters: it can support queries filtering by `Code` alone or by both properties, but is generally less useful when filtering only by `Category`.

## Index Name

By convention, EF Core names an index on `Product.Name` as `IX_Product_Name`. You can specify a different database index name without changing the indexed property.

**Data Annotations**

```csharp
[Index(nameof(Name), Name = "IX_Product_Custom")]
public class Product
{
    public int ProductId { get; set; }
    public string Name { get; set; } = null!;
}
```

**Fluent API**

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Product>()
        .HasIndex(p => p.Name)
        .HasDatabaseName("IX_Product_Custom");
}
```

Both configurations assign the database index the name `IX_Product_Custom`.

## Unique Index

By default, an index allows duplicate values. A unique index instead enforces uniqueness for the indexed column or combination of columns.

**Data Annotations**

```csharp
[Index(nameof(Code), IsUnique = true)]
public class Product
{
    public int ProductId { get; set; }
    public string Code { get; set; } = null!;
}
```

**Fluent API**

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Product>()
        .HasIndex(p => p.Code)
        .IsUnique();
}
```

With either configuration, the database rejects a second product with the same `Code`. How unique indexes handle `NULL` values depends on the database provider.

## Sort Order

Index columns are ascending by default. For composite indexes, you can make every column descending or specify a different direction for each one. Matching the index's column directions to a query's `ORDER BY` clause can improve sorting performance.

### All Columns Descending

**Data Annotations**

```csharp
[Index(nameof(Code), nameof(Category), AllDescending = true)]
public class Product
{
    public int ProductId { get; set; }
    public string Code { get; set; } = null!;
    public string Category { get; set; } = null!;
}
```

**Fluent API**

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Product>()
        .HasIndex(p => new { p.Code, p.Category })
        .IsDescending();
}
```

Both configurations set `Code` and `Category` to descending order.

### Different Sort Order per Column

**Data Annotations**

```csharp
[Index(nameof(Code), nameof(Category), IsDescending = new[] { false, true })]
public class Product
{
    public int ProductId { get; set; }
    public string Code { get; set; } = null!;
    public string Category { get; set; } = null!;
}
```

**Fluent API**

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Product>()
        .HasIndex(p => new { p.Code, p.Category })
        .IsDescending(false, true);
}
```

The Boolean values follow the order of the indexed properties: `false` keeps `Code` ascending, while `true` makes `Category` descending.

Index sort order can help the database execute matching queries efficiently, but it does not guarantee the order of returned rows. Use `OrderBy()` or `OrderByDescending()` when a query requires a specific result order.

## Filtered Index

A filtered index includes only rows that meet a specified condition. This can make the index smaller and more useful for queries that frequently access a subset of the table.

The following examples use a `Product` entity with `Code`, `Name`, `Price`, and `IsActive` properties.

For example, to index product codes only for active products, use `HasFilter()`:

**Fluent API — SQL Server**

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Product>()
        .HasIndex(p => p.Code)
        .HasFilter("[IsActive] = 1");
}
```

Here, `IsActive` is a `bool` property of `Product`. Only products with `IsActive = true` have entries in this index; all products remain in the table.

Filtered indexes are configured through Fluent API. The filter expression uses database-specific SQL syntax, so it must be adapted to the database provider.

## Included Properties

An index can include additional columns without making them part of its search key. This can help the database retrieve the required values directly from the index.

For example, to index `Code` while also including `Name` and `Price`, use `IncludeProperties()`:

**Fluent API — SQL Server**

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Product>()
        .HasIndex(p => p.Code)
        .IncludeProperties(p => new { p.Name, p.Price });
}
```

`Code` remains the indexed key, while `Name` and `Price` are included as non-key columns. This can improve queries that filter by `Code` and return those additional properties.

Included properties are configured through Fluent API, and support depends on the database provider.

## Index on Complex Type Properties

Starting with EF Core 11, an index can target a scalar property inside a complex type. The following illustrative snippet assumes that `Product.Details` is configured as a complex property and contains a scalar `Manufacturer` string property:

**Fluent API — EF Core 11**

```csharp
modelBuilder.Entity<Product>()
    .HasIndex(p => p.Details.Manufacturer);
```

This configuration is not supported in EF Core 10. See the [official EF Core index documentation](https://learn.microsoft.com/en-us/ef/core/modeling/indexes?tabs=data-annotations) for more examples, or our [Complex Type in EF Core](/modeling/complex-type) guide for an introduction to complex types.

## External Resources

The following videos complement the index configurations in this article by showing how database indexes work, how unique and filtered indexes affect data access, and how EF Core creates indexes through Fluent API and migrations.

Together, they provide both the database-level concepts behind indexing and a practical EF Core implementation.

### How SQL Indexes Actually Work (Step-by-Step) — Milan Jovanović

<iframe width="560" height="315" src="https://www.youtube.com/embed/teMXE6hzva0" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

Milan Jovanović explains how database indexes improve query execution using PostgreSQL examples and `EXPLAIN ANALYZE`. The tutorial demonstrates how B-tree indexes organize data, how the database chooses between different access methods, and how composite indexes can support queries involving multiple columns.

The video also explores partial indexes and the trade-offs of maintaining indexes, including additional storage and write overhead.

**Key timestamps:**

- [2:43 — Examining query execution plans](https://www.youtube.com/watch?v=teMXE6hzva0&t=163s)
- [4:21 — Understanding B-tree indexes](https://www.youtube.com/watch?v=teMXE6hzva0&t=261s)
- [11:01 — Exploring composite indexes](https://www.youtube.com/watch?v=teMXE6hzva0&t=661s)
- [12:45 — Understanding partial indexes](https://www.youtube.com/watch?v=teMXE6hzva0&t=765s)

Although the examples use PostgreSQL rather than EF Core, the concepts help explain why index configuration matters. This video provides useful background for the basic, composite, and filtered index examples in our article without duplicating their EF Core code.

### SQL Unique & Filtered Indexes (Visually Explained) | #SQL Course 37 — Data with Baraa

<iframe width="560" height="315" src="https://www.youtube.com/embed/PU2ajABOH-w" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

Data with Baraa demonstrates unique and filtered indexes in SQL Server, explaining how each serves a different purpose. The tutorial shows how unique indexes prevent duplicate key values and how filtered indexes apply only to rows that satisfy a specified condition.

Through SQL examples, the video illustrates index creation, duplicate-value rejection, and the use of a `WHERE` condition to define a filtered index.

**Key timestamps:**

- [0:18 — Introducing unique indexes](https://www.youtube.com/watch?v=PU2ajABOH-w&t=18s)
- [2:29 — Creating a unique index](https://www.youtube.com/watch?v=PU2ajABOH-w&t=149s)
- [5:53 — Introducing filtered indexes](https://www.youtube.com/watch?v=PU2ajABOH-w&t=353s)
- [7:52 — Creating a filtered index](https://www.youtube.com/watch?v=PU2ajABOH-w&t=472s)

This tutorial complements our `IsUnique()` and `HasFilter()` examples by showing the corresponding database-level behavior in SQL Server. It focuses on SQL rather than EF Core configuration, making it particularly useful for understanding what these Fluent API methods accomplish.

### EF Core + SQL Server in .NET 10 Web API: DbContext, Indexes & Migrations | Part 4 — Microcare Academy

<iframe width="560" height="315" src="https://www.youtube.com/embed/hzuVlJc5rYA" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

Microcare Academy demonstrates how to configure indexes in an EF Core application using .NET 10 and SQL Server. Within a larger example involving patient records, the tutorial creates a composite index with `HasIndex()` and applies `IsUnique()` to enforce uniqueness across a combination of properties.

The demonstration then generates an EF Core migration, examines the resulting indexes in SQL Server Management Studio, and tests how the database rejects duplicate values.

**Key timestamps:**

- [4:30 — Configuring indexes with `HasIndex()`](https://www.youtube.com/watch?v=hzuVlJc5rYA&t=270s)
- [4:57 — Enforcing uniqueness with `IsUnique()`](https://www.youtube.com/watch?v=hzuVlJc5rYA&t=297s)
- [6:07 — Examining indexes generated by EF Core migrations](https://www.youtube.com/watch?v=hzuVlJc5rYA&t=367s)
- [7:34 — Testing unique index enforcement with duplicate data](https://www.youtube.com/watch?v=hzuVlJc5rYA&t=454s)

This video connects the index configurations in our article to a working EF Core application. Its main value is demonstrating how `HasIndex()` and `IsUnique()` translate into database indexes and how those indexes behave after migrations are applied. The broader API setup provides context, while the timestamps focus on the sections directly relevant to indexing.

## Summary

EF Core supports simple and composite indexes through Data Annotations and Fluent API.

Key points:

- Use `[Index]` or `HasIndex()` to configure simple and composite indexes.
- Use `Name` or `HasDatabaseName()` to customize the database index name.
- Use `IsUnique` or `IsUnique()` to enforce uniqueness.
- Use `AllDescending`, `IsDescending`, or `IsDescending()` to configure index sort order.
- Use `HasFilter()` for filtered indexes and `IncludeProperties()` for included columns. Both require Fluent API and depend on database-provider support.

Choose indexes based on the queries your application runs. Indexes can improve query performance, but they also consume storage and add write overhead.

## Related Articles

- [Index Attribute](/configuration/data-annotation-attributes/index-attribute) — Configure indexes with Data Annotations.
- [HasIndex Method](/configuration/fluent-api/hasindex-method) — Configure indexes with Fluent API.
- [Complex Type in EF Core](/modeling/complex-type) — Understand the complex properties referenced in the EF Core 11 example.

## FAQ

### When should I use a composite index?

Use a composite index when queries frequently filter or sort by a particular combination of columns. The order of those columns matters: an index beginning with `Code` can often help queries filtering by `Code`, but generally not queries filtering only by `Category`.

### Can I configure a filtered index with Data Annotations?

No. Use Fluent API with `HasFilter()`. The filter is written in provider-specific SQL; the example in this article uses SQL Server syntax.

### What is the difference between indexed and included properties?

Indexed properties form the index key, which the database uses to locate or order entries. Included properties store additional values in the index without becoming part of that key. Support for included properties depends on the database provider.

### Do indexes guarantee the order of query results?

No. An index's sort direction can help the database execute a query, but it does not guarantee result order. Use `OrderBy()` or `OrderByDescending()` to request a specific order.