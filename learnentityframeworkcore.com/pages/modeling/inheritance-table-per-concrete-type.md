---
title: Table Per Concrete Type in EF Core (TPC) — Inheritance Mapping Explained
description: Learn how Table Per Concrete Type (TPC) inheritance works in Entity Framework Core, including SQL UNION behavior, performance trade-offs, duplicated inherited columns, and when to use it.
canonical: /modeling/inheritance-table-per-concrete-type
status: Published
lastmod: 2026-10-13
---

# Table Per Concrete Type in EF Core (TPC)

EF Core provides three inheritance mapping strategies:

- [Table Per Hierarchy (TPH)](/modeling/inheritance-table-per-hierarchy)
- [Table Per Type (TPT)](/modeling/inheritance-table-per-type)
- [Table Per Concrete Type (TPC)](/modeling/inheritance-table-per-concrete-type)

This page focuses on **Table Per Concrete Type (TPC)**.

## What is Table Per Concrete Type (TPC) in EF Core

Table Per Concrete Type (TPC) is an inheritance mapping strategy introduced in [EF Core 7](https://learn.microsoft.com/en-us/ef/core/what-is-new/ef-core-7.0/whatsnew#table-per-concrete-type-tpc-inheritance-mapping) where only concrete entity types are mapped to database tables.

Each concrete type maps to its own table, and each table contains all properties required by that type, including properties inherited from the base class.

When the base type is abstract, it has no table of its own. Unlike TPH, TPC does not require a stored discriminator column or columns for properties belonging exclusively to other concrete types.

<div class="image-outer"><img src="/images/efcore/inheritance/table-per-concrete-type.png" loading="lazy" alt="EF Core TPC Inheritance"></div>

[Online Example](https://dotnetfiddle.net/FusUAZ)

## TL;DR - EF Core TPC

- Uses one table per concrete type
- No table for the base type when it is abstract
- Each table contains:
  - base class properties
  - concrete type properties
- Avoids JOINs between inheritance tables when querying a concrete type
- Base properties are duplicated across tables
- Queries spanning multiple concrete types typically use `UNION ALL`
- Can benefit workloads where concrete types are queried independently, depending on query patterns and database performance

## Quick Mental Model

Think of TPC as flattening each concrete type into its own table.

Each table is self-contained and stores all properties needed for that concrete entity, without relying on a separate base table.

This avoids inheritance JOINs and columns reserved for other concrete types, but repeats inherited columns across tables. Queries spanning the hierarchy may need to combine those tables using `UNION ALL`.

## Schema Shape

In the `Animal` hierarchy below, the database contains separate `Cats` and `Dogs` tables. Each table has columns for the inherited properties alongside those specific to its concrete type, without storing columns exclusive to the other type. Because `Animal` is abstract, no `Animals` table is created.

## Configuring TPC in EF Core

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

You can configure TPC by calling `UseTpcMappingStrategy()` on the root entity and mapping each concrete type to its own table:

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

`UseTpcMappingStrategy()` selects TPC for the inheritance hierarchy. The `ToTable()` calls explicitly name the concrete tables; they are optional when the convention-generated table names are suitable.

`Cat` and `Dog` are included in the EF Core model through the `DbSet` properties and Fluent API configuration. EF Core does not automatically discover every subclass of `Animal`, so additional derived types must also be included in the model.

With this configuration, EF Core recognizes `Animal` as the abstract root of the hierarchy and `Cat` and `Dog` as concrete entity types. Both derived types inherit the `Id` key and the other base properties, but each is stored in its own table.

Because `Animal` is abstract, it has no physical table. If the base type were concrete, TPC would also map it to its own table.

A root `DbSet<Animal>` can still be exposed to query the hierarchy, even though `Animal` has no table. The SQL behavior of these queries is explained in the next section.

### TPC Generated Database Schema

With this configuration, EF Core generates one table per concrete type. The following example shows the schema for SQL Server:

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

There is no table for the abstract `Animal` base type.

Properties inherited from `Animal` (`Id`, `Name`, and `DateOfBirth`) appear in both tables. Each table also contains only the properties specific to its concrete type.

Although `Cats` and `Dogs` have separate primary key constraints, EF Core requires key values to be unique across the entire inheritance hierarchy. A `Cat` and a `Dog` must not have the same `Id`.

In SQL Server, EF Core uses the shared `AnimalSequence` by default to generate integer keys for TPC. Both tables obtain generated values from the same sequence, preventing collisions during normal sequence-based key generation.

The separate primary key constraints enforce uniqueness within each table, not across both tables. Therefore, manually assigned key values must also preserve uniqueness throughout the hierarchy.

Other approaches, such as sequence-based Hi-Lo generation or identity columns configured with non-overlapping seed and increment values, are possible. These alternatives depend on the database provider and require appropriate configuration.

**SQLite:** Unlike SQL Server, SQLite does not support database sequences or identity seed/increment configuration. Automatic integer key generation using these mechanisms is therefore unavailable for TPC. Applications can instead supply unique integer keys or use globally unique keys, such as GUIDs.

[Online Example](https://dotnetfiddle.net/VTeLuu)

## TPC Retrieving Derived Types via DbSet

EF Core allows querying derived entities directly by exposing `DbSet` properties for concrete types:

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

Each query targets the table mapped to its concrete type. Since `Cats` and `Dogs` contain both inherited and type-specific properties, EF Core can materialize these entities without joining separate inheritance tables.

You can also expose a root `DbSet<Animal>` to query the hierarchy, even though the abstract `Animal` class has no physical table:

```csharp
public DbSet<Animal> Animals { get; set; }
```

With this property added to `AnimalsDbContext`, you can query all concrete types or restrict the results to a particular derived type:

```csharp
using (var context = new AnimalsDbContext())
{
    var animals = context.Animals.ToList();

    var cats = context.Animals.OfType<Cat>().ToList();
    var dogs = context.Animals.OfType<Dog>().ToList();
}
```

The first query retrieves entities across the hierarchy. The `OfType<T>()` queries return only the requested derived type, allowing EF Core to access its concrete table without combining the sibling tables.

### SQL Behavior

TPC generates different SQL depending on the types included in a query:

- Queries targeting `Cats` or `Dogs` directly retrieve their data from the corresponding concrete table.
- Queries against `Animals` that retrieve the full hierarchy typically combine the concrete tables using `UNION ALL`.
- Queries such as `Animals.OfType<Cat>()` can access only `Cats`, without requiring a hierarchy-wide `UNION ALL`.

These queries do not require JOINs between base and derived inheritance tables. However, queries involving related entities may still require JOINs.

When EF Core combines different concrete tables, it projects a compatible set of columns. Properties belonging only to another concrete type are represented by `NULL` expressions, while projected type identifiers allow EF Core to materialize each entity correctly.

### Generated SQL

The following examples show SQL Server query shapes produced by the TPC mapping strategy. They follow the SQL inspected in the [online example](https://dotnetfiddle.net/M2ueD7); formatting is simplified for readability.

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

Both queries retrieve inherited and type-specific properties from one concrete table.

Querying the entire hierarchy through `context.Animals` combines `Cats` and `Dogs` using `UNION ALL`:

```sql
-- Querying the root type (context.Animals)
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

Each `SELECT` contributes rows from one concrete table. The `NULL` expressions align properties specific to the other type, while the projected `Discriminator` values identify the concrete entity types in the combined results.

Unlike TPH, TPC does not store a discriminator column in the `Cats` or `Dogs` tables. Here, `Discriminator` is an expression produced by the query.

Queries restricted to a derived type avoid the `UNION ALL`. In the online example, `OfType<T>()` queries also project a constant discriminator value:

```sql
-- Querying only Cats through the root set (context.Animals.OfType<Cat>())
SELECT [c].[Id],
       [c].[DateOfBirth],
       [c].[Name],
       [c].[IsIndoor],
       [c].[LivesRemaining],
       N'Cat' AS [Discriminator]
FROM [Cats] AS [c]

-- Querying only Dogs through the root set (context.Animals.OfType<Dog>())
SELECT [d].[Id],
       [d].[DateOfBirth],
       [d].[Name],
       [d].[Breed],
       [d].[IsGoodBoy],
       N'Dog' AS [Discriminator]
FROM [Dogs] AS [d]
```

These are two separate queries, not a single combined statement. Each reads only its concrete table. The projected discriminator is not a physical table column, and its presence in the SQL projection can differ from a direct `context.Cats` or `context.Dogs` query.

[Online Example](https://dotnetfiddle.net/M2ueD7)

## Performance Characteristics

TPC can be efficient when an application frequently queries individual concrete types. Since inherited and type-specific properties are stored together, a query for `Cat` or `Dog` can retrieve the entity from a single table without inheritance JOINs or filtering rows belonging to other concrete types.

This can offer an advantage over TPT, which may require JOINs to retrieve inherited properties, and over TPH, which stores multiple entity types in a shared table. However, simpler SQL does not automatically mean faster execution. Query performance also depends on filters, indexes, data distribution, and the database provider.

Queries spanning the hierarchy have different costs. As shown in the previous section, retrieving all `Animal` entities typically requires `UNION ALL` across concrete tables. More concrete types can mean more tables participating in the query, but the performance impact must be measured rather than assumed.

TPC also avoids storing columns belonging exclusively to other concrete types. This can be useful for hierarchies with many type-specific properties, although nullable properties such as `Name` and `Breed` remain nullable when the model allows it.

The trade-off is that inherited column definitions are repeated across concrete tables. This also has a maintenance cost: changing the database mapping of an inherited property may require schema changes in multiple tables.

**Performance should be measured against the application's actual queries.** A workload dominated by concrete-type queries may benefit from TPC, while one that frequently retrieves multiple derived types may favor TPH. For further guidance, see Microsoft's [inheritance mapping performance documentation](https://learn.microsoft.com/en-us/ef/core/performance/modeling-for-performance#inheritance-mapping).

## When to Use TPC in EF Core

TPC is a useful option when inheritance provides shared properties and behavior, but application operations primarily work with concrete entities independently.

For example, an application might use the `Animal` base class to share properties and behavior while handling cats and dogs through separate workflows. If it rarely needs to retrieve them together, TPC allows the database structure to reflect that concrete-type access pattern.

**Consider TPC when:**

- Most queries target individual concrete types rather than the entire hierarchy.
- Avoiding inheritance JOINs is beneficial for the application's measured workload.
- Concrete types have distinct properties, making separate tables a natural fit.
- Repeating inherited column definitions across tables is an acceptable schema and maintenance trade-off.

**Consider TPH or another mapping strategy when:**

- Queries frequently retrieve entities across multiple concrete types, making TPH's single-table structure potentially more suitable.
- Measured polymorphic query costs make combining multiple concrete tables unsuitable for the application's workload.
- Repeating inherited columns complicates schema evolution or conflicts with database design requirements.
- Database-enforced foreign keys must reference entities across the inheritance hierarchy.

**Foreign key limitation:** TPC supports relationships involving derived entities, but a relationship targeting the base `Animal` type cannot use a single conventional database foreign key constraint that references both `Cats` and `Dogs`. Each concrete type has its own table, and there is no shared `Animals` table for the constraint to reference.

For example, an entity with a `FavoriteAnimalId` property could reference either a cat or a dog. EF Core can manage this relationship through configured navigations and foreign key values. However, without a conventional database constraint spanning the concrete tables, the database cannot independently enforce that the referenced animal exists. This matters especially when data may also be modified outside EF Core. Relationships targeting one specific concrete table do not have this same limitation.

Key values must also remain unique across the hierarchy, as explained in the database schema section.

Ultimately, **choose TPC when its concrete-table structure matches the application's query patterns and its schema limitations are acceptable**. If polymorphic queries are common or database-enforced relationships to the hierarchy are essential, consider TPH first. For a side-by-side overview, see the [inheritance mapping comparison](/modeling/inheritance#quick-comparison).

## External Resources — Table Per Concrete Type (TPC)

The following videos complement this article from three perspectives: an in-depth discussion by the EF Core team of TPC's internal design and trade-offs, a practical walkthrough of its configuration and SQL Server migrations, and a visual comparison of the database schemas produced by TPH, TPT, and TPC.

Together, they connect the concrete tables, shared key generation, and SQL behavior explained above with real implementations, referential integrity considerations, and provider-dependent performance findings.

### Video 1 — .NET Data Community Standup

**TPH, TPT, and TPC Inheritance Mapping with EF Core**

<iframe width="560" height="315" src="https://www.youtube.com/embed/HaL6DKW1mrg?si=v4aXXMrSAdl28cqU" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

Arthur Vickers and Shay Rojansky examine TPC using an `Animal` inheritance hierarchy. They demonstrate how concrete tables store inherited properties, explain hierarchy-wide key generation using a shared sequence, and show why foreign key constraints targeting an abstract base type present additional challenges. They also inspect polymorphic queries using `UNION ALL` and compare storage and query performance across database providers.

**Key sections:**

- [22:50](https://www.youtube.com/watch?v=HaL6DKW1mrg&t=1370s) — TPC table structure and duplicated inherited columns
- [53:18](https://www.youtube.com/watch?v=HaL6DKW1mrg&t=3198s) — Unique keys across concrete tables and shared sequences
- [59:38](https://www.youtube.com/watch?v=HaL6DKW1mrg&t=3578s) — Foreign key constraints referencing a TPC hierarchy
- [1:12:22](https://www.youtube.com/watch?v=HaL6DKW1mrg&t=4342s) — Polymorphic queries and `UNION ALL`
- [1:14:30](https://www.youtube.com/watch?v=HaL6DKW1mrg&t=4470s) — TPC performance comparisons across database providers

This discussion is particularly valuable for understanding the design decisions behind TPC, especially the relationship between separate concrete tables, key uniqueness, and referential integrity. The performance experiments also illustrate why TPC cannot be ranked universally above TPH or TPT: results depend on query patterns, indexes, data distribution, and database providers. Recorded during EF Core 7 development, the video provides architectural context rather than definitive guidance on every current implementation detail.

### Video 2 — EF Core Inheritance: TPH vs TPT vs TPC with Real Examples

<iframe width="560" height="315" src="https://www.youtube.com/embed/dtqvdlVRG18?si=7Fwis9DyTMKw9zg5" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

Remigiusz demonstrates TPC using a `Vehicle` hierarchy containing `Car`, `Motorcycle`, and `Truck` entities. He configures `UseTpcMappingStrategy()`, examines the resulting SQL Server migration, and shows how the concrete tables contain both inherited and type-specific properties. The migration also introduces a shared `VehicleSequence` to generate identifiers across the hierarchy.

**Key sections:**

- [20:03](https://www.youtube.com/watch?v=dtqvdlVRG18&t=1203s) — TPC structure and abstract base class
- [20:38](https://www.youtube.com/watch?v=dtqvdlVRG18&t=1238s) — `UseTpcMappingStrategy()` configuration
- [21:09](https://www.youtube.com/watch?v=dtqvdlVRG18&t=1269s) — Inheritance JOINs and TPC trade-offs
- [22:42](https://www.youtube.com/watch?v=dtqvdlVRG18&t=1362s) — Shared sequence for hierarchy-wide unique IDs

Its main value is demonstrating how TPC configuration translates into a database migration and physical tables. It complements the first video's architectural discussion with a shorter implementation-focused walkthrough, particularly useful for understanding why a shared sequence appears when separate concrete tables use integer keys.

### Video 3 — Entity Framework 7 — Inheritance

<iframe width="560" height="315" src="https://www.youtube.com/embed/zctMt_rF9_U?si=Ov6fwQ8TTqKdPyfO" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

[Jasper Kent](/contributors/jasper-kent) demonstrates inheritance mapping using a `Pet` hierarchy with `Cat` and `Dog` entities. Starting with the default TPH strategy, he switches to TPT and then TPC, recreating the SQL Server database to show how the table structure changes. With TPC, each concrete table contains inherited and type-specific properties, while the abstract `Pet` class has no corresponding table.

**Key sections:**

- [09:03](https://www.youtube.com/watch?v=zctMt_rF9_U&t=543s) — TPC configuration with `UseTpcMappingStrategy()`
- [09:31](https://www.youtube.com/watch?v=zctMt_rF9_U&t=571s) — Generated TPC schema: concrete tables only
- [12:30](https://www.youtube.com/watch?v=zctMt_rF9_U&t=750s) — Conclusions and EF Core 7 inheritance strategies

This video is especially useful for visualizing how the same C# inheritance hierarchy produces different database schemas. It reinforces TPC's central storage trade-off: each concrete entity can be retrieved from its own table without inheritance JOINs, while inherited columns are repeated across concrete tables. Although recorded for EF Core 7, the fundamental mapping behavior remains relevant.

## Summary

Table Per Concrete Type (TPC) stores each concrete entity type in a separate table containing both inherited and type-specific properties. This allows concrete-type queries to avoid inheritance JOINs, while queries spanning the hierarchy may require `UNION ALL`.

TPC can be a good fit when an application primarily queries concrete types independently. However, repeated inherited columns, hierarchy-wide key uniqueness, and limitations on database-enforced foreign keys to the base type must also be considered.

The best choice depends on the application's query patterns, performance measurements, and schema requirements.

## Related Articles

- [Inheritance in EF Core](/modeling/inheritance) — Compare TPH, TPT, and TPC and choose a mapping strategy.
- [Table Per Hierarchy (TPH)](/modeling/inheritance-table-per-hierarchy) — Explore the default single-table strategy, particularly useful for polymorphic queries.
- [Table Per Type (TPT)](/modeling/inheritance-table-per-type) — Learn how inherited properties are stored in related tables and why inheritance JOINs are required.
