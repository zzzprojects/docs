---
title: EF Core Table Per Hierarchy (TPH) — Inheritance Mapping Explained
description: Learn how Table Per Hierarchy (TPH) inheritance works in Entity Framework Core, including discriminator columns, SQL behavior, performance trade-offs, and when to use it.
canonical: /inheritance/table-per-hierarchy
status: Published
lastmod: 2026-02-21
---

# EF Core — Table Per Hierarchy (TPH)

EF Core provides three inheritance mapping strategies:

- [Table Per Hierarchy (TPH)](/inheritance/table-per-hierarchy)
- [Table Per Type (TPT)](/inheritance/table-per-type)
- [Table Per Concrete Type (TPC)](/inheritance/table-per-concrete)

This page focuses on **Table Per Hierarchy (TPH)**.  

## What is Table Per Hierarchy (TPH) in EF Core

Table Per Hierarchy (TPH) is the **default inheritance mapping strategy in Entity Framework Core**.  

EF Core represents an object-oriented inheritance hierarchy using a **single database table**, where a **discriminator column** identifies the concrete derived type for each row.

All entities in the hierarchy share the same table structure, with unused columns set to `NULL` depending on the concrete type. Adding new derived types typically results in additional nullable columns being added to the same table.

<div class="image-outer"><img src="/images/efcore/inheritance/table-per-hierarchy.png" loading="lazy" alt="EF Core TPH Inheritance"></div>

[Online Example](https://dotnetfiddle.net/SitJWp)

## TL;DR — EF Core TPH

- Uses **one table** for the entire inheritance hierarchy  
- Relies on a **discriminator column** to identify derived types  
- **No JOINs** when querying derived entities  
- Generally provides **strong read performance**  
- Results in **wide tables** with nullable columns  
- Best suited for **stable hierarchies** with limited derived types  


## Quick Mental Model

Think of TPH as **one large table** where each row represents a different concrete type.  

The discriminator column tells EF Core **how to materialize each row into the correct CLR type**.

You gain performance by avoiding JOINs in polymorphic queries, but you trade schema normalization for simplicity and speed.

## Schema Shape

- A **single table** represents the entire hierarchy  
- Columns include:
  - Base class properties
  - All derived-type specific properties
- A **discriminator column** identifies the concrete type
- Many columns may be `NULL` depending on the row type


## Configuration in EF Core

### The Model

The following model is used consistently across all inheritance strategies:

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
````

Note: `Animal` does not have to be an abstract class.

### Explicit TPH Mapping with Discriminator

Although TPH is the default strategy, it can be configured explicitly using `UseTphMappingStrategy()`.

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Animal>(entity =>
    {
        entity.UseTphMappingStrategy();

        entity
            .HasDiscriminator<int>("AnimalType")
            .HasValue<Cat>(1)
            .HasValue<Dog>(2);
    });
	
    base.OnModelCreating(modelBuilder);
}
```

[Online Example](https://dotnetfiddle.net/WGJoXM)

The discriminator column tells EF Core which concrete CLR type should be materialized for each row.

### Registering Derived Types

Derived types can be registered either explicitly via `DbSet` or implicitly via `OnModelCreating`.

**Option 1 — Explicit DbSet registration**

```csharp
public DbSet<Animal> Animals { get; set; }
public DbSet<Cat> Cats { get; set; }
public DbSet<Dog> Dogs { get; set; }
```

[Online Example](https://dotnetfiddle.net/IxuJSt)

**Option 2 — Registration via `OnModelCreating`**

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Animal>().UseTphMappingStrategy();
    modelBuilder.Entity<Cat>();
    modelBuilder.Entity<Dog>();
	
    base.OnModelCreating(modelBuilder);
}
```

[Online Example](https://dotnetfiddle.net/ug1d4v)

Both approaches produce the **same database schema**.

## Retrieving Derived Types via DbSet


```csharp
var cats = context.Animals
    .OfType<Cat>()
    .ToList();

var dogs = context.Animals
    .OfType<Dog>()
    .ToList();
```

[Online Example](https://dotnetfiddle.net/Hh8mlE)

This is the traditional way to retrieve derived types when only the root `DbSet` is exposed.

In addition to `OfType<T>()`, EF Core also allows querying derived entities directly by exposing `DbSet` properties for concrete types.

```csharp
public class AnimalsDbContext : DbContext
{
    public DbSet<Animal> Animals { get; set; }
    public DbSet<Cat> Cats { get; set; }
    public DbSet<Dog> Dogs { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Animal>().UseTphMappingStrategy();
	
        base.OnModelCreating(modelBuilder);
    }
}

using (var context = new AnimalsDbContext())
{
    var cats = context.Cats.ToList();
    var dogs = context.Dogs.ToList();
}
```

[Online Example](https://dotnetfiddle.net/TISeBz)

This approach is less traditional but often **simpler and more expressive**, especially when working primarily with concrete types.


## SQL Behavior

* Queries against derived types are translated into **single-table SELECTs**
* Filtering is performed using the discriminator column
* **No JOINs** are generated
* All data is retrieved from one table


## Generated SQL (simplified)

The following examples show a simplified version of the SQL typically generated by EF Core for this mapping strategy.


```sql
-- Querying the root type (Animals DbSet)
SELECT
    [a].[Id],
    [a].[Name],
    [a].[DateOfBirth],
    [a].[AnimalType],
    [a].[IsIndoor],
    [a].[LivesRemaining],
    [a].[Breed],
    [a].[IsGoodBoy]
FROM [Animals] AS [a];
```

```sql
-- Querying only Cats (OfType<Cat>() / context.Cats)
SELECT
    [a].[Id],
    [a].[Name],
    [a].[DateOfBirth],
    [a].[IsIndoor],
    [a].[LivesRemaining]
FROM [Animals] AS [a]
WHERE [a].[AnimalType] = 1;
```

```sql
-- Querying only Dogs (OfType<Dog>() / context.Dogs)
SELECT
    [a].[Id],
    [a].[Name],
    [a].[DateOfBirth],
    [a].[Breed],
    [a].[IsGoodBoy]
FROM [Animals] AS [a]
WHERE [a].[AnimalType] = 2;
```

[Online Example](https://dotnetfiddle.net/2j34Pq)

## Performance Characteristics

* Reads are typically **very efficient** due to single-table access
* Writes are simple and fast
* Trade-offs include:
  * Wide tables
  * Many nullable columns
  * Schema changes when adding new derived types

Compared to other inheritance strategies, TPH usually offers the **best query performance for polymorphic workloads**, since it avoids JOINs entirely.


## When to Use vs When NOT to Use TPH

**Use TPH when:**

* Polymorphic queries are common
* Performance is critical
* The number of derived types is limited
* Schema simplicity is preferred

**Avoid TPH when:**

* The hierarchy contains many optional properties
* New derived types are added frequently
* Table width becomes difficult to manage


## Common Use Cases

TPH is commonly used when:

* Query performance matters more than normalization
* The inheritance hierarchy is stable
* Most derived types share a significant number of common properties


## External Resources — Table Per Hierarchy (TPH)

The following resources provide deeper insight into **EF Core inheritance mapping**, including internal design decisions, SQL behavior, and performance trade-offs.  

They are especially useful for understanding **why TPH is the default strategy**, how it compares to **TPT and TPC**, and what practical implications each approach has in real-world systems.

### Video 1 — .NET Data Community Standup  
**TPH, TPT, and TPC Inheritance Mapping with EF Core**  

<iframe width="560" height="315" src="https://www.youtube.com/embed/HaL6DKW1mrg?si=v4aXXMrSAdl28cqU" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

Arthur Vickers explains how EF Core maps inheritance hierarchies internally, with a strong focus on **SQL generation, performance characteristics, and storage trade-offs**. This talk provides essential context for understanding why TPH often delivers the best query performance.

**Key sections:**
- [09:00](https://www.youtube.com/watch?v=HaL6DKW1mrg&t=540s) — TPH concept and discriminator column
- [15:00](https://www.youtube.com/watch?v=HaL6DKW1mrg&t=900s) — Queries without JOINs
- [26:30](https://www.youtube.com/watch?v=HaL6DKW1mrg&t=1590s) — Performance comparison: TPT vs TPH
- [42:00](https://www.youtube.com/watch?v=HaL6DKW1mrg&t=2520s) — Sparse columns optimization (SQL Server)

### Video 2 — EF Core Inheritance: TPH vs TPT vs TPC with Real Examples  

<iframe width="560" height="315" src="https://www.youtube.com/embed/dtqvdlVRG18?si=7Fwis9DyTMKw9zg5" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

Remigiusz demonstrates inheritance mapping using **real models and migrations**, highlighting common configuration pitfalls. The TPH section clearly shows how a single-table strategy simplifies queries while introducing nullable columns as a trade-off.

**Key sections:**
- [03:08](https://www.youtube.com/watch?v=dtqvdlVRG18&t=188s) — Single table with discriminator column
- [04:31](https://www.youtube.com/watch?v=dtqvdlVRG18&t=271s) — Drawback: many NULL columns
- [06:31](https://www.youtube.com/watch?v=dtqvdlVRG18&t=391s) — Explicit `HasDiscriminator()` and `HasValue()` configuration
- [09:31](https://www.youtube.com/watch?v=dtqvdlVRG18&t=571s) — Abstract base class and discriminator configuration errors

### Video 3 — Entity Framework 7 — Inheritance  

<iframe width="560" height="315" src="https://www.youtube.com/embed/zctMt_rF9_U?si=Ov6fwQ8TTqKdPyfO" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

[Jasper](/contributors/jasper-kent) provides a high-level overview of inheritance strategies in EF Core 7, showing how TPH is applied by default and how discriminator columns affect the generated schema and query behavior.

**Key sections:**
- [04:30](https://www.youtube.com/watch?v=zctMt_rF9_U&t=270s) — Default TPH mapping (single table)
- [05:00](https://www.youtube.com/watch?v=zctMt_rF9_U&t=300s) — Discriminator column and NULL values
- [11:45](https://www.youtube.com/watch?v=zctMt_rF9_U&t=705s) — Explicit `UseTph()` / `UseTpt()` configuration
- [12:30](https://www.youtube.com/watch?v=zctMt_rF9_U&t=750s) — Conclusions and EF Core 7 considerations

## Summary & Next Steps

Table Per Hierarchy (TPH) is the simplest and most performant inheritance strategy in EF Core.

It excels at read-heavy, polymorphic workloads but trades normalization for speed and simplicity.

**Next steps:**

* Explore [Table Per Type (TPT)](/inheritance/table-per-type) for normalized schemas
* Explore [Table Per Concrete Type (TPC)](/inheritance/table-per-concrete) for write-optimized scenarios
* Review a [side-by-side comparison](/inheritance#quick-comparison) of all inheritance strategies in EF Core

Each inheritance strategy (TPH, TPT, TPC) optimizes for different trade-offs between performance, normalization, and schema flexibility.