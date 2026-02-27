---
title: EF Core Table Per Concrete Type (TPC) — Inheritance Mapping Explained
description: Learn how Table Per Concrete Type (TPC) inheritance works in Entity Framework Core, including SQL UNION behavior, performance trade-offs, columns duplication, and when to use it.
canonical: /inheritance/table-per-concrete-type
status: Published
lastmod: 2026-02-27
---

# EF Core - Table Per Concrete Type (TPC)

EF Core provides three inheritance mapping strategies:

- [Table Per Hierarchy (TPH)](/inheritance/table-per-hierarchy)
- [Table Per Type (TPT)](/inheritance/table-per-type)
- [Table Per Concrete Type (TPC)](/inheritance/table-per-concrete)

This page focuses on **Table Per Concrete Type (TPC)**.

## What is Table Per Concrete Type (TPC) in EF Core

Table Per Concrete Type (TPC) is an inheritance mapping strategy introduced in [EF Core 7](https://learn.microsoft.com/en-us/ef/core/what-is-new/ef-core-7.0/whatsnew#table-per-concrete-type-tpc-inheritance-mapping) where **only concrete entity types are mapped to database tables**.
  
Each concrete type maps to its own table, and **each table contains all properties required by that type**, including properties inherited from the base class.

The base type itself is **not mapped to a table when it is abstract**; this removes the need for discriminators and avoids the forced nullable columns required by other strategies like TPH.

<div class="image-outer"><img src="/images/efcore/inheritance/table-per-concrete-type.png" loading="lazy" alt="EF Core TPC Inheritance"></div>

[Online Example](https://dotnetfiddle.net/FusUAZ)

## TL;DR - EF Core TPC

- Uses **one table per concrete type**
- No table for the base type **when it is abstract**
- Each table contains:
  - base class properties
  - concrete type properties
- **No JOINs** when querying a concrete type
- Base properties are **duplicated across tables**
- Queries against the root type use **UNION ALL**
- Best suited for **read-heavy workloads** where concrete types are queried independently


## Quick Mental Model

Think of TPC as **flattening each concrete type into its own table**.  

Each table is self-contained and represents a complete entity.

You avoid JOINs and nullable columns, but you trade normalization for **duplicated base columns** and more complex queries due to UNION ALL across concrete tables when working with the root type.


## Schema Shape

- One table per **concrete type**
- No table for the base (abstract) type
- Each table contains:
  - base class properties
  - concrete class properties
- Base properties are duplicated across all concrete tables


## Configuration TPC in EF Core

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

### Configuring TPC Mapping

You can configure a TPC mapping by using `UseTpcMappingStrategy()` on the root entity and mapping each concrete type to its own table:


```csharp
public class AnimalsDbContext : DbContext
{
    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Animal>().UseTpcMappingStrategy();
        modelBuilder.Entity<Cat>().ToTable("Cats");
        modelBuilder.Entity<Dog>().ToTable("Dogs");
		
        base.OnModelCreating(modelBuilder);
    }
	
    public DbSet<Cat> Cats { get; set; }
    public DbSet<Dog> Dogs { get; set; }
}
```

[Online Example](https://dotnetfiddle.net/FusUAZ)

If the root entity is abstract, it is **not mapped to a database table**.

Usually, only the derived types (`Cats`, `Dogs`) are exposed.

The root `DbSet<Animal>` can still be exposed for querying, even though it does not map to a physical table. Exposing the root will generate `UNION ALL` queries when you query it.

### TPC Generated Database Schema

With this configuration, EF Core generates **one table per concrete type**:

```sql
CREATE SEQUENCE [AnimalSequence] START WITH 1 INCREMENT BY 1 NO CYCLE;

CREATE TABLE [Cats] (
    [Id] int NOT NULL DEFAULT (NEXT VALUE FOR [AnimalSequence]),
    [Name] nvarchar(max) NULL,
    [DateOfBirth] datetime2 NOT NULL,
    [IsIndoor] bit NOT NULL,
    [LivesRemaining] int NOT NULL,
    CONSTRAINT [PK_Cats] PRIMARY KEY ([Id])
);

CREATE TABLE [Dogs] (
    [Id] int NOT NULL DEFAULT (NEXT VALUE FOR [AnimalSequence]),
    [Name] nvarchar(max) NULL,
    [DateOfBirth] datetime2 NOT NULL,
    [Breed] nvarchar(max) NULL,
    [IsGoodBoy] bit NOT NULL,
    CONSTRAINT [PK_Dogs] PRIMARY KEY ([Id])
);
```

There is **no table** for the `Animal` base type.

Properties defined in the base class are repeated in each concrete table.

Key generation in TPC often relies on a shared sequence or Hi-Lo pattern to avoid key collisions across tables (provider-dependent).

[Online Example](https://dotnetfiddle.net/VTeLuu)

## TPC Retrieving Derived Types via DbSet

EF Core also allows querying derived entities directly by exposing `DbSet` properties for concrete types:

```csharp
public class AnimalsDbContext : DbContext
{
    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Animal>().UseTpcMappingStrategy();
        modelBuilder.Entity<Cat>().ToTable("Cats");
        modelBuilder.Entity<Dog>().ToTable("Dogs");
		
        base.OnModelCreating(modelBuilder);
    }
	
    public DbSet<Cat> Cats { get; set; }
    public DbSet<Dog> Dogs { get; set; }
}

using (var context = new AnimalsDbContext())
{
    var cats = context.Cats.ToList();
    var dogs = context.Dogs.ToList();
}
```

[Online Example](https://dotnetfiddle.net/XexlmG)

It is also possible to retrieve derived entities by exposing the root `Animals` set, but this is usually not recommended because it generates `UNION ALL` queries across the concrete tables.

Querying the concrete `DbSet` (`Cats`, `Dogs`) avoids the `UNION ALL` and directly targets the corresponding table.

### SQL Behavior

* Queries targeting a **single concrete type** access only its table
* No `JOIN` is generated for concrete-type queries
* When targeting the root type instead of a concrete type:
  * Queries require **`UNION ALL`** across concrete tables
  * Query complexity increases as the number of concrete types grows


### Generated SQL

The following examples show a simplified version of the SQL typically generated by EF Core for the TPC mapping strategy.

```sql
-- Querying a concrete type is a single-table SELECT (context.Cats)
SELECT [c].[Id],
       [c].[DateOfBirth],
       [c].[Name],
       [c].[IsIndoor],
       [c].[LivesRemaining]
FROM [Cats] AS [c]
```

```sql
-- Querying a concrete type is a single-table SELECT (context.Dogs)
SELECT [d].[Id],
       [d].[DateOfBirth],
       [d].[Name],
       [d].[Breed],
       [d].[IsGoodBoy]
FROM [Dogs] AS [d]
```

```sql
-- Querying the root type requires UNION ALL across all concrete tables (context.Animals)
SELECT [c].[Id],
       [c].[DateOfBirth],
       [c].[Name],
       [c].[IsIndoor],
       [c].[LivesRemaining],
       NULL AS [Breed],
       NULL AS [IsGoodBoy],
       N'Cat' AS [Discriminator]
FROM [Cats] AS [c]
UNION ALL
SELECT [d].[Id],
       [d].[DateOfBirth],
       [d].[Name],
       NULL AS [IsIndoor],
       NULL AS [LivesRemaining],
       [d].[Breed],
       [d].[IsGoodBoy],
       N'Dog' AS [Discriminator]
FROM [Dogs] AS [d]
```

```sql
-- Querying the root type with OfType<T>() generates a discriminator column
-- (context.Animals.OfType<Cat>(), context.Animals.OfType<Dog>())
SELECT [c].[Id],
       [c].[DateOfBirth],
       [c].[Name],
       [c].[IsIndoor],
       [c].[LivesRemaining],
N'Cat' AS [Discriminator]
FROM [Cats] AS [c]

SELECT [d].[Id],
       [d].[DateOfBirth],
       [d].[Name],
       [d].[Breed],
       [d].[IsGoodBoy],
       N'Dog' AS [Discriminator]
FROM [Dogs] AS [d]
```

[Online Example](https://dotnetfiddle.net/M2ueD7)

### Performance Characteristics

* Avoids JOINs between base and derived tables
* Eliminates nullable columns
* Base properties are duplicated across tables
* Performance is excellent for concrete-type queries compared to TPT, due to the absence of JOINs
* Queries against the root type use **UNION ALL**
* Root-type queries become more expensive as the number of concrete types increases

## TPC Common Use Cases

TPC is commonly used when:

* You want to avoid `JOIN` queries when retrieving concrete types
* Read performance for concrete types is important
* The hierarchy contains clearly independent concrete types
* You are fine with duplicated columns across tables
* You rarely query the root type

## When to Use vs When NOT to Use TPC

**Use TPC when:**

* Concrete types are queried independently
* JOINs must be avoided
* Read performance is critical
* The number of concrete types is limited

**Avoid TPC when:**

* Root-type queries are frequent
* The number of concrete types is large or unbounded
* Columns duplication is a concern


## External Resources — Table Per Concrete Type (TPC)

The following resources provide deeper insight into TPC mapping, including SQL behavior, performance trade-offs, and internal EF Core design decisions.


### Video 1 — .NET Data Community Standup

**TPH, TPT, and TPC Inheritance Mapping with EF Core**

<iframe width="560" height="315" src="https://www.youtube.com/embed/HaL6DKW1mrg?si=v4aXXMrSAdl28cqU" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

Arthur Vickers introduces TPC as a strategy added in EF Core 7. He explains how mapping only concrete types eliminates nullable columns, how queries rely on `UNION ALL`, and when TPC can outperform TPT.

**Key sections:**

* [22:50](https://www.youtube.com/watch?v=HaL6DKW1mrg&t=1370s) — TPC introduced in EF Core 7
* [35:00](https://www.youtube.com/watch?v=HaL6DKW1mrg&t=2100s) — Only concrete types mapped to tables
* [38:00](https://www.youtube.com/watch?v=HaL6DKW1mrg&t=2280s) — `UNION ALL` queries for multiple types
* [48:00](https://www.youtube.com/watch?v=HaL6DKW1mrg&t=2880s) — Hi-Lo and key generation considerations

### Video 2 — EF Core Inheritance: TPH vs TPT vs TPC with Real Examples

<iframe width="560" height="315" src="https://www.youtube.com/embed/dtqvdlVRG18?si=7Fwis9DyTMKw9zg5" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

Remigiusz demonstrates how TPC maps only concrete entities to tables, avoids JOINs, and often outperforms TPT in read-heavy scenarios, while introducing columns duplication.

**Key sections:**

* [20:03](https://www.youtube.com/watch?v=dtqvdlVRG18&t=1203s) — TPC and abstract base class
* [21:31](https://www.youtube.com/watch?v=dtqvdlVRG18&t=1291s) — `UseTpcMappingStrategy()` configuration
* [22:31](https://www.youtube.com/watch?v=dtqvdlVRG18&t=1351s) — Better performance than TPT (no JOINs)
* [23:31](https://www.youtube.com/watch?v=dtqvdlVRG18&t=1411s) — Shared sequence for unique IDs

### Video 3 — Entity Framework 7 — Inheritance

<iframe width="560" height="315" src="https://www.youtube.com/embed/zctMt_rF9_U?si=Ov6fwQ8TTqKdPyfO" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

[Jasper](/contributors/jasper-kent) provides a high-level overview of TPC mapping, explaining why it eliminates JOINs and NULL columns, and how it scales when adding new concrete types.

**Key sections:**

* [08:40](https://www.youtube.com/watch?v=zctMt_rF9_U&t=520s) — `UseTpcMappingStrategy()` configuration
* [09:04](https://www.youtube.com/watch?v=zctMt_rF9_U&t=544s) — Final schema: concrete tables only
* [12:30](https://www.youtube.com/watch?v=zctMt_rF9_U&t=750s) — Conclusions and EF Core 7 considerations

## Summary & Next Steps

Table Per Concrete Type (TPC) eliminates JOINs and nullable columns by mapping only concrete entities to tables.
It offers excellent performance for concrete-type queries, at the cost of columns duplication and more complex root-type queries.

**Next steps:**

* Review a [side-by-side comparison](/inheritance#quick-comparison) of TPH vs TPT vs TPC
* Review the [Table Per Hierarchy (TPH)](/inheritance/table-per-hierarchy) default inheritance
* Review the [Table Per Type (TPT)](/inheritance/table-per-type) for normalized schemas
* Choose the strategy that best fits your **query patterns and schema constraints**