---
title: EF Core Table Per Concrete Type (TPC) — Inheritance Mapping Explained
description: Learn how Table Per Concrete Type (TPC) inheritance works in Entity Framework Core, including SQL UNION behavior, performance trade-offs, data duplication, and when to use it.
canonical: /inheritance/table-per-concrete-type
status: Published
lastmod: 2026-02-21
---

# EF Core — Table Per Concrete Type (TPC)

EF Core provides three inheritance mapping strategies:

- [Table Per Hierarchy (TPH)](/inheritance/table-per-hierarchy)
- [Table Per Type (TPT)](/inheritance/table-per-type)
- [Table Per Concrete Type (TPC)](/inheritance/table-per-concrete)

This page focuses on **Table Per Concrete Type (TPC)**.

[Online Example](https://dotnetfiddle.net/FusUAZ)

## What is Table Per Concrete Type (TPC) in EF Core

Table Per Concrete Type (TPC) is an inheritance mapping strategy introduced in **EF Core 7.0** where **only concrete entity types are mapped to database tables**.  
Each concrete type maps to its own table, and **each table contains all properties required by that type**, including properties inherited from the base class.

The base type itself is **not mapped to a table when it is abstract**, which removes the need for discriminators and avoids nullable columns.

<div class="image-outer"><img src="/images/efcore/inheritance/table-per-concrete-type.png" loading="lazy" alt="EF Core TPC Inheritance"></div>

## TL;DR — EF Core TPC

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

You avoid JOINs and nullable columns, but you trade normalization for **data duplication** and more complex queries due to UNION ALL across concrete tables when working with the root type.


## Schema Shape

- One table per **concrete type**
- No table for the base (abstract) type
- Each table contains:
  - base class properties
  - concrete class properties
- Base properties are duplicated across all concrete tables


## Configuration in EF Core

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

The root DbSet can still be exposed for querying, even though it does not map to a physical table.


### Configuring TPC Mapping

To enable TPC mapping, call `UseTpcMappingStrategy()` on the root entity and map each concrete type to its own table:

```csharp
public class AnimalsDbContext : DbContext
{
    public DbSet<Animal> Animals { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Animal>().UseTpcMappingStrategy();
        modelBuilder.Entity<Cat>().ToTable("Cats");
        modelBuilder.Entity<Dog>().ToTable("Dogs");
		
        base.OnModelCreating(modelBuilder);
    }
}
```

[Online Example](https://dotnetfiddle.net/FusUAZ)

If the root entity is abstract, it is **not mapped to a database table**.


## Generated Database Schema

With this configuration, EF Core generates **one table per concrete type**:

```sql
CREATE TABLE [dbo].[Dogs] (
    [Id] INT NOT NULL,
    [Name] NVARCHAR(MAX) NULL,
    [DateOfBirth] DATETIME2 NOT NULL,
    [BarkSound] NVARCHAR(MAX) NULL,
    CONSTRAINT [PK_Dogs] PRIMARY KEY ([Id])
);

CREATE TABLE [dbo].[Cats] (
    [Id] INT NOT NULL,
    [Name] NVARCHAR(MAX) NULL,
    [DateOfBirth] DATETIME2 NOT NULL,
    [MeowSound] NVARCHAR(MAX) NULL,
    CONSTRAINT [PK_Cats] PRIMARY KEY ([Id])
);
```

There is **no table** for the `Animal` base type.
Properties defined in the base class are repeated in each concrete table.
Key generation in TPC often relies on a shared sequence or Hi-Lo pattern to avoid key collisions across tables (provider-dependent).


## Retrieving Derived Types via DbSet

```csharp
public class AnimalsDbContext : DbContext
{
    public DbSet<Animal> Animals { get; set; }
    public DbSet<Cat> Cats { get; set; }
    public DbSet<Dog> Dogs { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Animal>().UseTpcMappingStrategy();
        modelBuilder.Entity<Cat>().ToTable("Cats");
        modelBuilder.Entity<Dog>().ToTable("Dogs");
		
        base.OnModelCreating(modelBuilder);
    }
}

using (var context = new AnimalsDbContext())
{
    var cats = context.Cats.ToList();
    var dogs = context.Dogs.ToList();
}
```

[Online Example](https://dotnetfiddle.net/XexlmG)


## SQL Behavior

* Queries targeting a **single concrete type** access only its table
* No JOINs are generated for concrete-type queries
* Queries targeting the root type require **UNION ALL** across concrete tables
* Query complexity increases as the number of concrete types grows


## Generated SQL (simplified)

The following examples show a simplified version of the SQL typically generated by EF Core for this mapping strategy.


```sql
-- Querying a concrete type is a single-table SELECT
SELECT
    [c].[Id],
    [c].[Name],
    [c].[DateOfBirth],
    [c].[MeowSound]
FROM [Cats] AS [c];
```

```sql
-- Querying a concrete type is a single-table SELECT
SELECT
    [d].[Id],
    [d].[Name],
    [d].[DateOfBirth],
    [d].[BarkSound]
FROM [Dogs] AS [d];
```

```sql
-- Querying the root type requires UNION ALL across all concrete tables
SELECT
    [c].[Id],
    [c].[Name],
    [c].[DateOfBirth],
    CAST(1 AS int) AS [AnimalType],
    [c].[MeowSound] AS [MeowSound],
    CAST(NULL AS nvarchar(max)) AS [BarkSound]
FROM [Cats] AS [c]

UNION ALL

SELECT
    [d].[Id],
    [d].[Name],
    [d].[DateOfBirth],
    CAST(2 AS int) AS [AnimalType],
    CAST(NULL AS nvarchar(max)) AS [MeowSound],
    [d].[BarkSound] AS [BarkSound]
FROM [Dogs] AS [d];
```

[Online Example](https://dotnetfiddle.net/M2ueD7)

## Data Insertion

To insert data using TPC mapping, add concrete entities and call `SaveChanges()`:

```csharp
using (var context = new AnimalsDbContext())
{
    var cat = new Cat
    {
        Name = "Fluffy",
        DateOfBirth = new DateTime(2020, 1, 1),
        MeowSound = "Meow"
    };

    context.Animals.Add(cat);

    var dog = new Dog
    {
        Name = "Buddy",
        DateOfBirth = new DateTime(2019, 6, 15),
        BarkSound = "Woof"
    };

    context.Animals.Add(dog);

    context.SaveChanges();
}
```

EF Core determines the correct table based on the concrete entity type.


## Data Retrieval

To retrieve concrete entities, use `OfType<T>()`:

```csharp
using (var context = new AnimalsDbContext())
{
    var cats = context.Animals
        .OfType<Cat>()
        .ToList();

    var dogs = context.Animals
        .OfType<Dog>()
        .ToList();
}
```

When querying the root type, EF Core aggregates results from all concrete tables.

## Performance Characteristics

* Avoids JOINs between base and derived tables
* Eliminates nullable columns
* Base properties are duplicated across tables
* Queries against the root type use **UNION ALL**
* Performance is excellent for concrete-type queries compared to TPT, due to the absence of JOINs
* Root-type queries become more expensive as the number of concrete types increases


## When to Use vs When NOT to Use TPC

**Use TPC when:**

* Concrete types are queried independently
* JOINs must be avoided
* Read performance is critical
* The number of concrete types is limited

**Avoid TPC when:**

* Root-type queries are frequent
* The number of concrete types is large or unbounded
* Data duplication is a concern


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

Remigiusz demonstrates how TPC maps only concrete entities to tables, avoids JOINs, and often outperforms TPT in read-heavy scenarios, while introducing data duplication.

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
It offers excellent performance for concrete-type queries, at the cost of data duplication and more complex root-type queries.

**Next steps:**

* Review a **side-by-side comparison of TPH vs TPT vs TPC**
* Choose the strategy that best fits your **query patterns and schema constraints**