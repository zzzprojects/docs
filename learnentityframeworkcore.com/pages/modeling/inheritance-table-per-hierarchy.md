---
title: Table Per Hierarchy in EF Core (TPH) — Inheritance Mapping Explained
description: Learn how Table Per Hierarchy (TPH) inheritance works in Entity Framework Core, including discriminator columns, SQL behavior, performance trade-offs, and when to use it.
canonical: /modeling/inheritance-table-per-hierarchy
status: Published
lastmod: 2026-10-13
---

# Table Per Hierarchy in EF Core (TPH)

EF Core provides three inheritance mapping strategies:

- [Table Per Hierarchy (TPH)](/modeling/inheritance-table-per-hierarchy)
- [Table Per Type (TPT)](/modeling/inheritance-table-per-type)
- [Table Per Concrete Type (TPC)](/modeling/inheritance-table-per-concrete-type)

This page focuses on **Table Per Hierarchy (TPH)**.

## What is Table Per Hierarchy (TPH) in EF Core

Table Per Hierarchy (TPH) is the **default inheritance mapping strategy in Entity Framework Core**.

EF Core represents an object-oriented inheritance hierarchy using a **single database table**, where a **discriminator column** identifies the concrete derived type for each row.

All entities in the hierarchy share the same table. Columns for properties specific to derived types are nullable so that rows of other types can contain `NULL`. Adding new derived types with additional properties typically adds columns to this table.

<div class="image-outer"><img src="/images/efcore/inheritance/table-per-hierarchy.png" loading="lazy" alt="EF Core TPH Inheritance"></div>

This diagram shows the conventional `Discriminator` column. The Fluent API example below uses a custom integer discriminator named `AnimalType` instead.

[Online Example](https://dotnetfiddle.net/SitJWp)

## TL;DR - EF Core TPH

- Uses **one table** for the entire inheritance hierarchy
- Relies on a **discriminator column** to identify derived types
- Avoids **JOINs between base and derived inheritance tables**
- Often supports **efficient polymorphic queries**
- Can produce **wide tables** with nullable columns for derived-type properties
- Adding derived types with new properties can require schema changes


## Quick Mental Model

Think of TPH as **one large table** storing all entities in an inheritance hierarchy.

The discriminator column tells EF Core **how to materialize each row into the correct CLR type**.

TPH avoids the joins that TPT uses to combine base and derived tables, but stores properties from different entity types in the same table.

## Schema Shape

- A **single table** represents the entire hierarchy
- Columns include:
   - Base class properties
   - All derived-type specific properties
   - A **discriminator column** identifies the concrete type
- Many columns may be `NULL` depending on the row type


## Configuring TPH in EF Core

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
```

### Fluent API Configuration

TPH is the default strategy, so `UseTphMappingStrategy()` is optional. You can use it to make the strategy explicit and customize the discriminator with `HasDiscriminator()` and `HasValue()`:

```csharp
public class AnimalsDbContext : DbContext
{
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
	
    public DbSet<Animal> Animals { get; set; }
}
```

[Online Example](https://dotnetfiddle.net/WGJoXM)

This configuration maps `Animal`, `Cat`, and `Dog` to the same table. The integer discriminator `AnimalType` identifies `Cat` with value `1` and `Dog` with value `2`.

The root `DbSet<Animal>` can query the hierarchy; separate `DbSet` properties for derived types are optional.

**Unmapped discriminator values:** If the database contains a discriminator value that is not mapped to an entity type, EF Core throws an exception when it tries to materialize that row. For intentionally incomplete mappings, configure the discriminator with `IsComplete(false)` so EF Core filters queries, including queries of the root type, to mapped discriminator values.

### TPH Generated Database Schema

With this configuration, EF Core maps the entire inheritance hierarchy to **one table**. The following example shows the table structure for SQL Server:

```sql
CREATE TABLE [Animals] (
    [Id] int NOT NULL IDENTITY,
    [Name] nvarchar(max) NULL,
    [DateOfBirth] datetime2 NOT NULL,
    [AnimalType] int NOT NULL,
    [IsIndoor] bit NULL,
    [LivesRemaining] int NULL,
    [Breed] nvarchar(max) NULL,
    [IsGoodBoy] bit NULL,
    CONSTRAINT [PK_Animals] PRIMARY KEY ([Id])
);
```

The table includes the base class properties and all derived-type specific properties (stored as **nullable** columns).

[Online Example](https://dotnetfiddle.net/OSxCmt)

## Querying Derived Types in TPH

You can query a specific derived type through the root `Animals` set using `OfType<T>()`.

```csharp
public class AnimalsDbContext : DbContext
{
    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Animal>().UseTphMappingStrategy();
        modelBuilder.Entity<Cat>();
        modelBuilder.Entity<Dog>();
	
        base.OnModelCreating(modelBuilder);
    }
	
    public DbSet<Animal> Animals { get; set; }
}

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

[Online Example](https://dotnetfiddle.net/Hh8mlE)

No separate `DbSet<Cat>` or `DbSet<Dog>` properties are needed for these queries.

In addition to `OfType<T>()`, EF Core also allows querying derived entities directly by exposing `DbSet` properties for concrete types.

```csharp
public class AnimalsDbContext : DbContext
{
    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Animal>().UseTphMappingStrategy();
	
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
}
```

[Online Example](https://dotnetfiddle.net/TISeBz)

Exposing `DbSet<Cat>` and `DbSet<Dog>` lets you query each derived type directly and includes them in the EF Core model by convention. EF Core does not automatically scan for every subclass in an inheritance hierarchy, so derived types must be included through model configuration or discovery.


### SQL Behavior

* Queries against derived types are translated into **single-table SELECTs**
* Filtering is performed using the discriminator column
* No additional **JOINs** are needed to reconstruct the inheritance hierarchy
* Queries involving only this hierarchy retrieve its data from the shared table


### Generated SQL

The two querying examples above use the conventional `Discriminator` column with values such as `Cat` and `Dog`. The simplified SQL Server queries below instead use the custom `AnimalType` discriminator configured earlier (`1` for `Cat` and `2` for `Dog`).


```sql
-- Querying the root type (context.Animals)
SELECT [a].[Id],
       [a].[AnimalType],
       [a].[DateOfBirth],
       [a].[Name],
       [a].[IsIndoor],
       [a].[LivesRemaining],
       [a].[Breed],
       [a].[IsGoodBoy]
FROM [Animals] AS [a]
```

```sql
-- Querying only Cats (context.Animals.OfType<Cat>() / context.Cats)
SELECT [a].[Id],
       [a].[AnimalType],
       [a].[DateOfBirth],
       [a].[Name],
       [a].[IsIndoor],
       [a].[LivesRemaining]
FROM [Animals] AS [a]
WHERE [a].[AnimalType] = 1
```

```sql
-- Querying only Dogs (context.Animals.OfType<Dog>() / context.Dogs)
SELECT [a].[Id],
       [a].[AnimalType],
       [a].[DateOfBirth],
       [a].[Name],
       [a].[Breed],
       [a].[IsGoodBoy]
FROM [Animals] AS [a]
WHERE [a].[AnimalType] = 2
```

In this example, the root query reads from the shared table without a discriminator filter, while the `Cat` and `Dog` queries filter by their mapped `AnimalType` values. TPH does not require JOINs between inheritance tables.

[Online Example](https://dotnetfiddle.net/2j34Pq)

### Performance Characteristics

* Queries over the inheritance hierarchy can benefit from **single-table access**
* Each entity is stored in one row of the shared table
* Trade-offs include:
  * Wide tables
  * Many nullable columns
  * Schema changes when adding new derived types with additional properties

TPH often performs well for polymorphic queries because it avoids joins between inheritance tables. Actual performance depends on the queries, indexes, data volume, and database provider.

## When to Use TPH in EF Core

TPH is often a good choice when:

* Polymorphic queries are common
* A single table simplifies access to the inheritance hierarchy
* Derived types share many properties, limiting type-specific columns
* The shared-table schema fits the application's maintenance requirements

**Consider TPT or TPC when:**

* Numerous type-specific columns make the shared table difficult to manage
* Frequently adding derived types with new properties makes shared-table schema changes difficult to manage
* Query patterns or database design requirements favor another mapping strategy

For a broader comparison, see [Inheritance in EF Core](/modeling/inheritance#quick-comparison).

## External Resources — Table Per Hierarchy (TPH)

The following videos complement this article from three perspectives: an in-depth discussion by the EF Core team of inheritance mapping and its performance trade-offs, a practical walkthrough of TPH configuration and queries, and a visual comparison of the database schemas produced by TPH, TPT, and TPC.

Together, they connect the single-table model and discriminator behavior explained above with real SQL output, database migrations, and the decisions involved in choosing an inheritance strategy.

### Video 1 — .NET Data Community Standup  
**TPH, TPT, and TPC Inheritance Mapping with EF Core**  

<iframe width="560" height="315" src="https://www.youtube.com/embed/HaL6DKW1mrg?si=v4aXXMrSAdl28cqU" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

Arthur Vickers and Shay Rojansky examine how EF Core maps inheritance hierarchies, using an `Animal` model to demonstrate TPH, TPT, and TPC. They show the generated SQL for polymorphic and derived-type queries, compare TPH and TPT performance, and investigate storage trade-offs, including SQL Server sparse columns.

**Key sections:**
- [12:24](https://www.youtube.com/watch?v=HaL6DKW1mrg&t=744s) — TPH concept and single-table mapping
- [31:32](https://www.youtube.com/watch?v=HaL6DKW1mrg&t=1892s) — TPH query examples and generated SQL
- [42:01](https://www.youtube.com/watch?v=HaL6DKW1mrg&t=2521s) — Performance comparison: TPT vs TPH
- [1:04:04](https://www.youtube.com/watch?v=HaL6DKW1mrg&t=3844s) — Sparse columns optimization (SQL Server)

This resource is especially useful for understanding why TPH can simplify polymorphic queries without assuming that it is always the fastest strategy. The benchmarks illustrate how query patterns, table structure, and database providers affect the comparison, while the sparse-column discussion adds a SQL Server-specific perspective on managing wide TPH tables.

### Video 2 — EF Core Inheritance: TPH vs TPT vs TPC with Real Examples  

<iframe width="560" height="315" src="https://www.youtube.com/embed/dtqvdlVRG18?si=7Fwis9DyTMKw9zg5" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

Remigiusz demonstrates TPH through a `Vehicle` hierarchy containing cars, motorcycles, and trucks. He configures an enum-based discriminator using `HasDiscriminator()` and `HasValue()`, compares `OfType<T>()` with direct access through derived `DbSet` properties, and inspects the resulting migrations and database records.

**Key sections:**
- [03:08](https://www.youtube.com/watch?v=dtqvdlVRG18&t=188s) — Single table with discriminator column
- [04:31](https://www.youtube.com/watch?v=dtqvdlVRG18&t=271s) — Drawback: many NULL columns
- [06:31](https://www.youtube.com/watch?v=dtqvdlVRG18&t=391s) — Explicit `HasDiscriminator()` and `HasValue()` configuration
- [09:31](https://www.youtube.com/watch?v=dtqvdlVRG18&t=571s) — Abstract base class and discriminator configuration errors

Its main value is showing how Fluent API configuration translates into an actual database table and working queries. The enum discriminator also offers a useful alternative to the integer-based configuration used in this article, without requiring another complete example in the main text.

### Video 3 — Entity Framework 7 — Inheritance  

<iframe width="560" height="315" src="https://www.youtube.com/embed/zctMt_rF9_U?si=Ov6fwQ8TTqKdPyfO" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

[Jasper Kent](/contributors/jasper-kent) demonstrates inheritance mapping using a `Pet` hierarchy with `Cat` and `Dog` entities. Starting with EF Core's default TPH mapping, he inspects the SQL Server table, its discriminator and derived-type columns, then switches to TPT and TPC to show how the database schema changes.

**Key sections:**
- [04:30](https://www.youtube.com/watch?v=zctMt_rF9_U&t=270s) — Default TPH mapping (single table)
- [05:00](https://www.youtube.com/watch?v=zctMt_rF9_U&t=300s) — Discriminator column and NULL values
- [11:45](https://www.youtube.com/watch?v=zctMt_rF9_U&t=705s) — Explicit `UseTphMappingStrategy()` configuration
- [12:30](https://www.youtube.com/watch?v=zctMt_rF9_U&t=750s) — Conclusions and EF Core 7 considerations

This video is particularly useful for visualizing the structural differences between the three strategies. Although recorded for EF Core 7, its TPH examples remain relevant and reinforce an important design trade-off: keeping the hierarchy in one shared table versus distributing its data across multiple tables.

## Summary

Table Per Hierarchy (TPH) is the default inheritance mapping strategy in EF Core, storing the hierarchy in one table.

It can simplify polymorphic queries by avoiding joins between inheritance tables, while introducing nullable columns for derived-type properties.

Each inheritance strategy (TPH, TPT, TPC) optimizes for different trade-offs between performance, normalization, and schema flexibility.

## Related Articles

* Explore [Table Per Type (TPT)](/modeling/inheritance-table-per-type) for normalized schemas
* Explore [Table Per Concrete Type (TPC)](/modeling/inheritance-table-per-concrete-type) for separate concrete-type tables without inheritance joins
* Review a [side-by-side comparison](/modeling/inheritance#quick-comparison) of all inheritance strategies in EF Core