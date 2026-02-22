---
title: EF Core Table Per Type (TPT) — Inheritance Mapping Explained
description: Learn how Table Per Type (TPT) inheritance works in Entity Framework Core, including schema normalization, SQL JOIN behavior, performance trade-offs, and when to use it.
canonical: /inheritance/table-per-type
status: Published
lastmod: 2026-02-21
---

# EF Core — Table Per Type (TPT)

EF Core provides three inheritance mapping strategies:

- [Table Per Hierarchy (TPH)](/inheritance/table-per-hierarchy)
- [Table Per Type (TPT)](/inheritance/table-per-type)
- [Table Per Concrete Type (TPC)](/inheritance/table-per-concrete)

This page focuses on **Table Per Type (TPT)**.


## What is Table Per Type (TPT) in EF Core

Table Per Type (TPT) is an inheritance mapping strategy in Entity Framework Core where **each type in the inheritance hierarchy is mapped to its own database table**.  
The base type is stored in a separate table, and each derived type is stored in an additional table linked to the base table through a foreign key.

This approach produces a **fully normalized schema**, but introduces additional complexity in query translation and execution. 
This complexity becomes more noticeable as the hierarchy grows or when polymorphic queries are common


## TL;DR — EF Core TPT

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


## Configuration in EF Core

### The Model

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

```csharp
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
```

By convention, EF Core generates table names automatically.
You can override table names explicitly if needed. This is optional but often used to make the resulting schema explicit.


### Registering Derived Types

Derived types can be registered in multiple ways, depending on how you want to query them.

**Option 1 — Expose only the root DbSet**

```csharp
public DbSet<Animal> Animals { get; set; }
```

Derived entities are accessed using `OfType<T>()`.

**Option 2 — Expose DbSet properties for derived types**

```csharp
public DbSet<Animal> Animals { get; set; }
public DbSet<Cat> Cats { get; set; }
public DbSet<Dog> Dogs { get; set; }
```

Exposing derived DbSets does **not** change the database schema.
It only affects how queries are written and expressed in code.


## Retrieving Derived Types via DbSet

```csharp
public class AnimalsDbContext : DbContext
{
    public DbSet<Animal> Animals { get; set; }
    public DbSet<Cat> Cats { get; set; }
    public DbSet<Dog> Dogs { get; set; }

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
}

using (var context = new AnimalsDbContext())
{
    var cats = context.Cats.ToList();
    var dogs = context.Dogs.ToList();
}
```


## SQL Behavior

* Queries against derived types generate **JOINs** between:

  * the base table
  * one or more derived tables
* JOINs are required even when querying a **single concrete type**
* Query complexity increases as the hierarchy grows


## Generated SQL (simplified)

```sql
-- Querying Cats requires a JOIN between the base table and the derived table
SELECT
    [a].[Id],
    [a].[Name],
    [a].[DateOfBirth],
    [c].[IsIndoor],
    [c].[LivesRemaining]
FROM [Animals] AS [a]
INNER JOIN [Cats] AS [c] ON [a].[Id] = [c].[Id];
```

```sql
-- Querying Dogs requires a JOIN between the base table and the derived table
SELECT
    [a].[Id],
    [a].[Name],
    [a].[DateOfBirth],
    [d].[Breed],
    [d].[IsGoodBoy]
FROM [Animals] AS [a]
INNER JOIN [Dogs] AS [d] ON [a].[Id] = [d].[Id];
```

```sql
-- Querying the root type typically results in LEFT JOINs across all derived tables
SELECT
    [a].[Id],
    [a].[Name],
    [a].[DateOfBirth],
    [c].[IsIndoor],
    [c].[LivesRemaining],
    [d].[Breed],
    [d].[IsGoodBoy]
FROM [Animals] AS [a]
LEFT JOIN [Cats] AS [c] ON [a].[Id] = [c].[Id]
LEFT JOIN [Dogs] AS [d] ON [a].[Id] = [d].[Id];
```


## Performance Characteristics

* Produces **highly normalized schemas**
* Queries involving derived types require **JOINs**
* Query cost increases with:

  * hierarchy depth
  * number of derived types
* Typically slower than TPH for polymorphic, read-heavy workloads due to JOIN overhead


## Common Use Cases

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
* [11:45](https://www.youtube.com/watch?v=zctMt_rF9_U&t=705s) — Explicit `UseTpt()` / `UseTph()` configuration

## Summary & Next Steps

Table Per Type (TPT) prioritizes **schema normalization** by splitting an inheritance hierarchy across multiple tables.
While this approach produces clean relational models, it introduces JOIN-heavy queries and additional performance cost.

**Next steps:**

* Explore **Table Per Concrete Type (TPC)** for write-optimized scenarios
* Review a side-by-side comparison of **TPH vs TPT vs TPC**

```