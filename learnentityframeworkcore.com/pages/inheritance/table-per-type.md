---
title: EF Core Table Per Type (TPT) — Inheritance Mapping Explained
description: Learn how Table Per Type (TPT) inheritance works in Entity Framework Core, including schema normalization, SQL JOIN behavior, performance trade-offs, and when to use it.
canonical: /inheritance/table-per-type
status: Published
lastmod: 2026-02-27
---

# EF Core - Table Per Type (TPT)

EF Core provides three inheritance mapping strategies:

- [Table Per Hierarchy (TPH)](/inheritance/table-per-hierarchy)
- [Table Per Type (TPT)](/inheritance/table-per-type)
- [Table Per Concrete Type (TPC)](/inheritance/table-per-concrete)

This page focuses on **Table Per Type (TPT)**.

## What is Table Per Type (TPT) in EF Core

Table Per Type (TPT) is an inheritance mapping strategy introduced in [EF Core 5](https://learn.microsoft.com/en-us/ef/core/what-is-new/ef-core-5.0/whatsnew#table-per-type-tpt-mapping) where **each type in the inheritance hierarchy is mapped to its own database table**.
  
The base type is stored in a separate table, and each derived type is stored in an additional table linked to the base table through a foreign key.

This approach produces a **fully normalized schema**, but introduces additional complexity in query translation and execution. 

This complexity becomes more noticeable as the hierarchy grows or when polymorphic queries are common

<div class="image-outer"><img src="/images/efcore/inheritance/table-per-type.png" loading="lazy" alt="EF Core TPT Inheritance"></div>

[Online Example](https://dotnetfiddle.net/2JYcbs)

## TL;DR - EF Core TPT

- Uses **one table for the base type** and **one table per derived type**
- Derived tables are linked to the base table via **foreign keys**
- Queries against derived types require **JOINs**
- Produces a **clean and normalized schema**
- Query cost increases with hierarchy depth
- Best suited for scenarios where **schema normalization is prioritized over read performance**


## Quick Mental Model

Think of TPT as **splitting an inheritance hierarchy vertically across multiple tables**.  

To materialize a derived entity, EF Core must **join the base table with one or more derived tables**.

You gain schema normalization, but you pay for it with **more complex SQL and higher query cost compared to TPH, especially for polymorphic queries**.


## Schema Shape

- One table for the **base type**
- One table for **each derived type**
- Derived tables reference the base table using **foreign keys**
- Queries frequently require **multiple JOINs**


## Configuration TPT in EF Core

### The Model

The following model represents an inheritance hierarchy using an abstract base class:

```csharp
public abstract class Animal
{
    public int Id { get; set; }
    public string? Name { get; set; }
    public DateTime DateOfBirth { get; set; }
}

public class Cat : Animal
{
    public bool IsIndoor { get; set; }
    public int LivesRemaining { get; set; }
}

public class Dog : Animal
{
    public string? Breed { get; set; }
    public bool IsGoodBoy { get; set; }
}
```

### Configuring TPT Mapping

You can configure a TPT mapping by using `UseTptMappingStrategy()`:

```csharp
public class AnimalsDbContext : DbContext
{
    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Animal>()
            .UseTptMappingStrategy()
            .ToTable("Animals");

        modelBuilder.Entity<Cat>()
            .ToTable("Cats");

        modelBuilder.Entity<Dog>()
            .ToTable("Dogs");
			
        base.OnModelCreating(modelBuilder);
    }
	
    public DbSet<Animal> Animals { get; set; }
    public DbSet<Cat> Cats { get; set; }
    public DbSet<Dog> Dogs { get; set; }
}
```

[Online Example](https://dotnetfiddle.net/2JYcbs)

By convention, EF Core generates table names automatically.

You can override table names explicitly if needed. This is optional but often used to make the resulting schema more explicit.

Usually, the base set (`Animals`) and all derived types (`Cats`, `Dogs`) are exposed.

### TPT Generated Database Schema

With this configuration, EF Core generates **one table for the base type** and **one table per derived type**:

```sql
CREATE TABLE [Animals] (
    [Id] int NOT NULL IDENTITY,
    [Name] nvarchar(max) NULL,
    [DateOfBirth] datetime2 NOT NULL,
    CONSTRAINT [PK_Animals] PRIMARY KEY ([Id])
);

CREATE TABLE [Cats] (
    [Id] int NOT NULL,
    [IsIndoor] bit NOT NULL,
    [LivesRemaining] int NOT NULL,
    CONSTRAINT [PK_Cats] PRIMARY KEY ([Id]),
    CONSTRAINT [FK_Cats_Animals_Id] FOREIGN KEY ([Id]) REFERENCES [Animals] ([Id]) ON DELETE CASCADE
);

CREATE TABLE [Dogs] (
    [Id] int NOT NULL,
    [Breed] nvarchar(max) NULL,
    [IsGoodBoy] bit NOT NULL,
    CONSTRAINT [PK_Dogs] PRIMARY KEY ([Id]),
    CONSTRAINT [FK_Dogs_Animals_Id] FOREIGN KEY ([Id]) REFERENCES [Animals] ([Id]) ON DELETE CASCADE
);
```

It produces a **clean and normalized schema**, and the Derived tables are linked to the base table via **foreign keys**.

[Online Example](https://dotnetfiddle.net/3nzgSS)

## TPT Retrieving Derived Types via DbSet

You can either query the concrete `DbSet` directly or use the base set with `OfType<T>()` to query a specific derived type.

```csharp
public class AnimalsDbContext : DbContext
{
    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Animal>()
            .UseTptMappingStrategy()
            .ToTable("Animals");

        modelBuilder.Entity<Cat>()
            .ToTable("Cats");

        modelBuilder.Entity<Dog>()
            .ToTable("Dogs");
			
        base.OnModelCreating(modelBuilder);
    }
	
    public DbSet<Animal> Animals { get; set; }
    public DbSet<Cat> Cats { get; set; }
    public DbSet<Dog> Dogs { get; set; }
}

using (var context = new AnimalsDbContext())
{
	
    var cats = context.Cats.ToList();
    var dogs = context.Dogs.ToList();
	
    var cats2 = context.Animals
        .OfType<Cat>()
        .ToList();

    var dogs2 = context.Animals
        .OfType<Dog>()
        .ToList();
}
```

[Online Example](https://dotnetfiddle.net/ygZGdN)

`OfType<T>()` is especially useful when you only expose the base `DbSet` (for example, only `Animals`). In that case, it allows you to filter and retrieve a specific derived type without exposing separate `DbSet` properties.

### SQL Behavior

* Queries against derived types generate **JOINs** between:
  * the base table
  * one or more derived tables
* JOINs are required even when querying a **single concrete type**
* Query complexity increases as the hierarchy grows


### Generated SQL

The following examples show a simplified version of the SQL typically generated by EF Core for the TPT mapping strategy.


```sql
-- Querying the root type typically results in LEFT JOINs across all derived tables (context.Animals)
SELECT [a].[Id],
       [a].[DateOfBirth],
       [a].[Name],
       [c].[IsIndoor],
       [c].[LivesRemaining],
       [d].[Breed],
       [d].[IsGoodBoy],
       CASE
           WHEN [d].[Id] IS NOT NULL THEN N'Dog'
           WHEN [c].[Id] IS NOT NULL THEN N'Cat'
       END AS [Discriminator]
FROM [Animals] AS [a]
LEFT JOIN [Cats] AS [c] ON [a].[Id] = [c].[Id]
LEFT JOIN [Dogs] AS [d] ON [a].[Id] = [d].[Id]
```

```sql
-- Querying the root type with OfType<T>() generates a discriminator column
-- (context.Animals.OfType<Cat>(), context.Animals.OfType<Dog>())
SELECT [a].[Id],
       [a].[DateOfBirth],
       [a].[Name],
       [c].[IsIndoor],
       [c].[LivesRemaining],
       CASE
           WHEN [c].[Id] IS NOT NULL THEN N'Cat'
       END AS [Discriminator]
FROM [Animals] AS [a]
LEFT JOIN [Cats] AS [c] ON [a].[Id] = [c].[Id]
WHERE [c].[Id] IS NOT NULL

SELECT [a].[Id],
       [a].[DateOfBirth],
       [a].[Name],
       [d].[Breed],
       [d].[IsGoodBoy],
       CASE
           WHEN [d].[Id] IS NOT NULL THEN N'Dog'
       END AS [Discriminator]
FROM [Animals] AS [a]
LEFT JOIN [Dogs] AS [d] ON [a].[Id] = [d].[Id]
WHERE [d].[Id] IS NOT NULL
```

```sql
-- Querying Cats requires a JOIN between the base table and the derived table (context.Cats)
SELECT [a].[Id],
       [a].[DateOfBirth],
       [a].[Name],
       [c].[IsIndoor],
       [c].[LivesRemaining]
FROM [Animals] AS [a]
INNER JOIN [Cats] AS [c] ON [a].[Id] = [c].[Id]
```

```sql
-- Querying Dogs requires a JOIN between the base table and the derived table (context.Dogs)
SELECT [a].[Id],
       [a].[DateOfBirth],
       [a].[Name],
       [d].[Breed],
       [d].[IsGoodBoy]
FROM [Animals] AS [a]
INNER JOIN [Dogs] AS [d] ON [a].[Id] = [d].[Id]
```

[Online Example](https://dotnetfiddle.net/Kjj9b1)

### Performance Characteristics

* Produces **highly normalized schemas**
* Queries involving derived types require **JOINs**
* Query cost increases with:

  * hierarchy depth
  * number of derived types
* Typically slower than TPH for polymorphic, read-heavy workloads due to JOIN overhead


## TPT Common Use Cases

TPT is commonly used when:

* Schema normalization is a priority
* Derived types have significantly different sets of properties
* The inheritance hierarchy is small and relatively stable
* Write performance is less critical than schema clarity


## When to Use vs When NOT to Use TPT

**Use TPT when:**

* Schema normalization is a priority
* Derived types have significantly different properties
* The inheritance hierarchy is small and stable
* Read performance is not the primary concern

**Avoid TPT when:**

* Polymorphic queries are frequent
* Performance is critical
* The hierarchy is deep or frequently changing


## External Resources — Table Per Type (TPT)

The following resources provide deeper insight into **TPT mapping**, including SQL behavior, performance trade-offs, and internal EF Core design decisions.


### Video 1 — .NET Data Community Standup

**TPH, TPT, and TPC Inheritance Mapping with EF Core**

<iframe width="560" height="315" src="https://www.youtube.com/embed/HaL6DKW1mrg?si=v4aXXMrSAdl28cqU" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

Arthur Vickers explains how TPT maps each type in the hierarchy to its own table and analyzes the **JOIN-heavy SQL** generated by EF Core. He contrasts TPT with TPH and discusses the resulting performance implications.

**Key sections:**

* [18:00](https://www.youtube.com/watch?v=HaL6DKW1mrg&t=1080s) — Introduction to TPT mapping
* [22:00](https://www.youtube.com/watch?v=HaL6DKW1mrg&t=1320s) — Multiple tables in the hierarchy
* [24:00](https://www.youtube.com/watch?v=HaL6DKW1mrg&t=1440s) — LEFT JOIN explosion in queries
* [26:30](https://www.youtube.com/watch?v=HaL6DKW1mrg&t=1590s) — Performance comparison: TPT vs TPH

### Video 2 — EF Core Inheritance: TPH vs TPT vs TPC with Real Examples

<iframe width="560" height="315" src="https://www.youtube.com/embed/dtqvdlVRG18?si=7Fwis9DyTMKw9zg5" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

Remigiusz demonstrates how TPT splits an inheritance hierarchy across multiple tables linked by foreign keys, producing a clean schema while introducing additional JOINs during query execution.

**Key sections:**

* [15:10](https://www.youtube.com/watch?v=dtqvdlVRG18&t=910s — Introduction to TPT mapping
* [16:31](https://www.youtube.com/watch?v=dtqvdlVRG18&t=991s) — `UseTptMappingStrategy()` configuration
* [18:31](https://www.youtube.com/watch?v=dtqvdlVRG18&t=1111s) — Final schema: base table + derived tables
* [19:31](https://www.youtube.com/watch?v=dtqvdlVRG18&t=1171s) — JOINs generated by EF Core queries

### Video 3 — Entity Framework 7 — Inheritance

<iframe width="560" height="315" src="https://www.youtube.com/embed/zctMt_rF9_U?si=Ov6fwQ8TTqKdPyfO" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

[Jasper](/contributors/jasper-kent) provides a high-level overview of TPT mapping, explaining how it improves normalization while increasing query complexity compared to TPH.

**Key sections:**

* [05:40](https://www.youtube.com/watch?v=zctMt_rF9_U&t=340s) — TPT configuration in `OnModelCreating`
* [06:35](https://www.youtube.com/watch?v=zctMt_rF9_U&t=395s) — Resulting schema: base + derived tables
* [11:45](https://www.youtube.com/watch?v=zctMt_rF9_U&t=705s) — Explicit `UseTptMappingStrategy()` configuration

## Summary & Next Steps

Table Per Type (TPT) prioritizes **schema normalization** by splitting an inheritance hierarchy across multiple tables.

While this approach produces clean relational models, it introduces JOIN-heavy queries and additional performance cost.

**Next steps:**

* Explore [Table Per Concrete Type (TPC)](/inheritance/table-per-concrete) for write-optimized scenarios
* Review a [side-by-side comparison](/inheritance#quick-comparison) of **TPH vs TPT vs TPC**
* Review the [Table Per Hierarchy (TPH)](/inheritance/table-per-hierarchy) default inheritance