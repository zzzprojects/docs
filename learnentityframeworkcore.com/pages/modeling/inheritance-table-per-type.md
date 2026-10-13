---
title: Table Per Type in EF Core (TPT) — Inheritance Mapping Explained
description: Learn how Table Per Type (TPT) inheritance works in Entity Framework Core, including Fluent API configuration, database schema, SQL JOINs, and performance trade-offs.
canonical: /modeling/inheritance-table-per-type
status: Published
lastmod: 2026-10-13
---

# Table Per Type in EF Core (TPT)

EF Core provides three inheritance mapping strategies:

- [Table Per Hierarchy (TPH)](/modeling/inheritance-table-per-hierarchy)
- [Table Per Type (TPT)](/modeling/inheritance-table-per-type)
- [Table Per Concrete Type (TPC)](/modeling/inheritance-table-per-concrete-type)

This page focuses on **Table Per Type (TPT)**.

## What is Table Per Type (TPT) in EF Core

Table Per Type (TPT) is an inheritance mapping strategy introduced in [EF Core 5](https://learn.microsoft.com/en-us/ef/core/what-is-new/ef-core-5.0/whatsnew#table-per-type-tpt-mapping) where each type in an inheritance hierarchy is mapped to its own database table.

The base table stores properties declared by the base type, while each derived table stores its own declared properties. A derived table is linked to its base table through a primary key that also acts as a foreign key.

Retrieving complete derived entities typically requires JOINs, especially in polymorphic queries.

<div class="image-outer"><img src="/images/efcore/inheritance/table-per-type.png" loading="lazy" alt="EF Core TPT Inheritance"></div>

[Online Example](https://dotnetfiddle.net/2JYcbs)

## TL;DR - EF Core TPT

- Uses **one table for the base type and one table per derived type**
- Links derived tables to their base tables through **primary key and foreign key constraints**
- Typically requires **JOINs to materialize complete derived entities**
- Separates inherited and type-specific properties across tables
- Can increase query cost, particularly for polymorphic queries
- Suits scenarios where separate tables for each type justify the additional query overhead

## Quick Mental Model

Think of TPT as splitting an inheritance hierarchy vertically across multiple tables.

A derived entity has one identity even though its data spans multiple tables. Unlike [TPH](/modeling/inheritance-table-per-hierarchy), which uses one table for the hierarchy, TPT keeps the inherited and type-specific values in separate rows linked by that identity.

## Schema Shape

- One table represents the base type.
- Each derived type has its own table containing its declared properties.
- Each derived table has a primary key that also references its base table.

## Configuring TPT in EF Core

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

### Fluent API Configuration

TPH is EF Core's default inheritance mapping strategy. To use TPT, configure the strategy with `UseTptMappingStrategy()` on the root entity type:

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

This configuration maps `Animal` to `Animals`, `Cat` to `Cats`, and `Dog` to `Dogs`, while preserving the inheritance relationship in the EF Core model.

Each `Cat` or `Dog` retains the inherited properties `Id`, `Name`, and `DateOfBirth`. In the database, those properties are stored in `Animals`, while the derived tables store their type-specific properties and the shared key.

`UseTptMappingStrategy()` selects TPT for the hierarchy, and EF Core can generate table names by convention. Alternatively, mapping each type to its own table with `ToTable()` also establishes TPT.

The derived `DbSet<Cat>` and `DbSet<Dog>` properties are optional for querying the hierarchy. However, the derived types must be included in the EF Core model, either through `DbSet` discovery or explicit configuration. EF Core does not automatically discover every CLR subclass.

### TPT Generated Database Schema

With this configuration, EF Core generates one table for the base type and one table per derived type. The following SQL shows the corresponding SQL Server table structure:

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

The `Animals` table stores the shared properties, while `Cats` and `Dogs` store only their type-specific properties.

In each derived table, `Id` acts as both the **primary key** and a **foreign key** referencing `Animals.Id`. This preserves one entity identity across the tables rather than creating separate entities with independent identities.

For example, a `Cat` with `Id = 1` is represented by a row in `Animals` containing its inherited properties and a matching row in `Cats` containing `IsIndoor` and `LivesRemaining`.

This separation also has a database constraint limitation: because inherited and derived properties are stored in different tables, a composite foreign key constraint or index cannot directly combine columns from both tables.

[Online Example](https://dotnetfiddle.net/3nzgSS)

## Querying Derived Types in TPT

You can query a concrete derived type directly through its `DbSet`, or use `OfType<T>()` on the base set to retrieve entities of a specific derived type.

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

Both approaches retrieve entities of the requested derived type. `context.Cats` and `context.Dogs` query their corresponding entity sets directly, while `OfType<T>()` filters the base query to a specific CLR type.

`OfType<T>()` is especially useful when only the base `DbSet<Animal>` is exposed. Separate derived `DbSet` properties are not required, provided the derived types are included in the EF Core model.

In either case, a complete `Cat` or `Dog` entity includes properties stored in both `Animals` and its derived table. EF Core retrieves the required data to materialize the entity, although the generated SQL can differ between the two approaches.

### SQL Behavior

Because TPT distributes inherited and type-specific properties across tables, the SQL depends on which types and properties the query needs:

- **Root queries:** Querying `context.Animals` can use `LEFT JOIN`s to retrieve data from multiple derived tables and identify the concrete entity types.
- **Derived-type queries:** Queries for complete `Cat` or `Dog` entities combine the base table with the corresponding derived table. Direct `DbSet` queries and `OfType<T>()` may use different JOIN patterns.
- **Projections:** Queries selecting only particular properties may need fewer tables than queries materializing complete entities.

Unlike TPH, TPT does not require a discriminator column stored in the base table. For polymorphic results, EF Core can identify concrete types from matching rows in derived tables, sometimes using a calculated discriminator expression in the SQL result.

The following examples show how these differences appear in SQL.

### Generated SQL

The SQL Server examples below illustrate the queries produced for the `Animal`, `Cat`, and `Dog` hierarchy. They preserve the relevant JOIN patterns and discriminator expressions observed using `ToQueryString()` in the accompanying online example. Exact SQL can vary by EF Core version, provider, and query shape.

**Querying the root type**

Querying `context.Animals` retrieves entities from the hierarchy. EF Core uses `LEFT JOIN`s to access both derived tables and a calculated expression to distinguish `Cat` and `Dog` instances.

```sql
-- Querying the root type (context.Animals)
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

The `CASE` expression produces a discriminator value in the query result. It is not a discriminator column stored in `Animals`, as in a conventional TPH mapping.

**Filtering the base query with `OfType<T>()`**

When retrieving `Cat` or `Dog` entities through `context.Animals.OfType<T>()`, the SQL observed in the online example uses a `LEFT JOIN`, a filter requiring a matching derived row, and a calculated discriminator.

```sql
-- Querying Cats through context.Animals.OfType<Cat>()
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
```

```sql
-- Querying Dogs through context.Animals.OfType<Dog>()
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

The `WHERE` condition restricts each query to rows with a matching derived entity. These expressions illustrate the translation observed for this model; `OfType<T>()` does not universally require this exact SQL structure or a calculated discriminator.

**Querying concrete entity sets directly**

Querying `context.Cats` or `context.Dogs` directly produces a different pattern in the verified example: an `INNER JOIN` between the base table and the selected derived table.

```sql
-- Querying Cats directly (context.Cats)
SELECT [a].[Id],
       [a].[DateOfBirth],
       [a].[Name],
       [c].[IsIndoor],
       [c].[LivesRemaining]
FROM [Animals] AS [a]
INNER JOIN [Cats] AS [c] ON [a].[Id] = [c].[Id]
```

```sql
-- Querying Dogs directly (context.Dogs)
SELECT [a].[Id],
       [a].[DateOfBirth],
       [a].[Name],
       [d].[Breed],
       [d].[IsGoodBoy]
FROM [Animals] AS [a]
INNER JOIN [Dogs] AS [d] ON [a].[Id] = [d].[Id]
```

Here, the `INNER JOIN` combines the inherited properties from `Animals` with the type-specific properties from `Cats` or `Dogs`.

These examples demonstrate an important distinction: `context.Cats` and `context.Animals.OfType<Cat>()` retrieve the same entity type but can generate different SQL. The same applies to `Dog`. Both approaches need the relevant base and derived data when materializing complete entities, but their JOIN patterns are not necessarily identical.

[Online Example](https://dotnetfiddle.net/Kjj9b1)

### Performance Characteristics

TPT separates inherited and type-specific properties across tables, but this can increase the work required to retrieve entities.

- **Polymorphic queries** may join multiple derived tables to retrieve type-specific properties and determine which concrete entities to materialize.
- **Queries for complete derived entities** commonly combine the base table with the relevant derived table, even when only one concrete type is requested.
- **Deeper or wider hierarchies** can involve additional tables and JOINs, depending on which entities and properties the query requires.
- **Projections** may reduce the required table access when they select only properties available from a subset of the mapped tables.

Compared with TPH, TPT often has higher query overhead for polymorphic, read-heavy workloads because data must be combined across inheritance tables. However, the actual performance impact depends on query shape, data volume, indexes, hierarchy structure, and database provider.

These query costs are the main performance consideration when evaluating TPT against other inheritance mapping strategies.

## When to Use TPT

TPT is worth considering when your database design requires separate tables for each type in an inheritance hierarchy. However, that structure comes with query and write overhead, so it should generally be chosen for a specific requirement rather than for schema organization alone.

**Consider TPT when:**

- **You are mapping an existing database.** The schema already stores base and derived properties in separate tables connected by primary key and foreign key relationships.
- **Separate tables are a database requirement.** External design constraints or database conventions require inherited and type-specific properties to remain in their respective tables.
- **The workload can accommodate the additional cost.** The application's queries and writes have been evaluated, and TPT performs adequately for its expected usage.

**Prefer another strategy when:**

- **Polymorphic queries are frequent or performance-sensitive.** [TPH](/modeling/inheritance-table-per-hierarchy) usually offers a simpler query structure for retrieving entities across the hierarchy.
- **Most queries retrieve one concrete type.** [TPC](/modeling/inheritance-table-per-concrete-type) may be worth evaluating because complete concrete entities are stored in their corresponding tables.
- **The hierarchy is deep or has many derived types.** Materializing entities can involve more tables, increasing the importance of measuring query performance.

A small, stable hierarchy with distinct type-specific properties can make TPT easier to manage, but these characteristics alone do not establish that it is the best mapping strategy.

If you are free to choose the database structure, start by evaluating TPH or TPC against your query patterns. Choose TPT when its separate-table design provides a concrete benefit or satisfies a requirement that outweighs the additional overhead.

## External Resources — Table Per Type (TPT)

The following videos complement this article from three perspectives: an in-depth discussion by the EF Core team of TPT's SQL and performance trade-offs, a practical walkthrough of Fluent API configuration and migrations, and a visual comparison of the database schemas produced by TPH, TPT, and TPC.

Together, they show how TPT distributes inherited properties across related tables, how EF Core retrieves complete entities, and what to consider when choosing this mapping strategy.

### Video 1 — .NET Data Community Standup

**TPH, TPT, and TPC Inheritance Mapping with EF Core**

<iframe width="560" height="315" src="https://www.youtube.com/embed/HaL6DKW1mrg?si=v4aXXMrSAdl28cqU" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

Arthur Vickers and Shay Rojansky examine inheritance mapping using an `Animal` hierarchy with multiple levels of derived types. They demonstrate how TPT creates separate tables linked by shared keys, configure the strategy through Fluent API, and inspect the JOINs generated for polymorphic and derived-type queries. They also compare TPT and TPH performance using a concrete benchmark.

**Key sections:**

- [18:19](https://www.youtube.com/watch?v=HaL6DKW1mrg&t=1099s) — TPT table structure and shared primary keys
- [36:59](https://www.youtube.com/watch?v=HaL6DKW1mrg&t=2219s) — Configuring TPT with `ToTable()` and `UseTptMappingStrategy()`
- [40:18](https://www.youtube.com/watch?v=HaL6DKW1mrg&t=2418s) — TPT queries and generated JOINs
- [44:18](https://www.youtube.com/watch?v=HaL6DKW1mrg&t=2658s) — TPH vs. TPT performance benchmark

This resource is especially valuable for understanding the trade-offs behind TPT rather than just its configuration. The discussion also examines how storing one entity across multiple tables affects inserts and generated keys, while emphasizing that performance results depend on the hierarchy, query patterns, and workload. Although the session was recorded during the EF Core 7 preview period, its core explanations of TPT mapping remain relevant.

### Video 2 — EF Core Inheritance: TPH vs TPT vs TPC with Real Examples

<iframe width="560" height="315" src="https://www.youtube.com/embed/dtqvdlVRG18?si=7Fwis9DyTMKw9zg5" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

Remigiusz demonstrates TPT using a `Vehicle` hierarchy with `Car`, `Motorcycle`, and `Truck` entities in a .NET API. He configures `UseTptMappingStrategy()`, generates a migration, and inspects the resulting SQL Server tables and foreign key relationships. He then creates vehicles and retrieves derived entities to illustrate how EF Core combines properties stored in separate tables.

**Key sections:**

- [15:10](https://www.youtube.com/watch?v=dtqvdlVRG18&t=910s) — TPT concept and separate tables for each type
- [16:12](https://www.youtube.com/watch?v=dtqvdlVRG18&t=972s) — Configuring TPT with `UseTptMappingStrategy()`
- [17:58](https://www.youtube.com/watch?v=dtqvdlVRG18&t=1078s) — Generated schema and derived-table relationships
- [19:16](https://www.youtube.com/watch?v=dtqvdlVRG18&t=1156s) — Querying derived entities across base and derived tables

Its main value is connecting the Fluent API configuration to a working application and its database schema. The migration and database inspection make the shared-key relationships visible, while the retrieval examples demonstrate how inherited and type-specific properties are returned together. This provides a practical complement to the SQL-focused examples in the article.

### Video 3 — Entity Framework 7 — Inheritance

<iframe width="560" height="315" src="https://www.youtube.com/embed/zctMt_rF9_U?si=Ov6fwQ8TTqKdPyfO" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

[Jasper Kent](/contributors/jasper-kent) demonstrates inheritance mapping using an abstract `Pet` class with `Cat` and `Dog` entities. He first configures TPT by mapping the derived classes to separate tables with `ToTable()`, then inspects how SQL Server stores their inherited and type-specific properties. Later, he demonstrates the explicit `UseTptMappingStrategy()` API while comparing TPT with TPH and TPC.

**Key sections:**

- [06:12](https://www.youtube.com/watch?v=zctMt_rF9_U&t=372s) — TPT mapping through derived-type `ToTable()` configuration
- [07:01](https://www.youtube.com/watch?v=zctMt_rF9_U&t=421s) — TPT database schema: shared base table and derived tables
- [11:45](https://www.youtube.com/watch?v=zctMt_rF9_U&t=705s) — Explicit `UseTptMappingStrategy()` configuration

This video is particularly useful for visualizing how the same inheritance hierarchy changes when switching mapping strategies. It illustrates the distinction between TPT's separate base and derived tables, TPH's single shared table, and TPC's concrete-type tables. Although it uses EF Core 7, the demonstrated TPT concepts and Fluent API configurations remain applicable to current EF Core versions.

## Summary

Table Per Type (TPT) maps each type in an inheritance hierarchy to a separate database table. Shared properties are stored in the base table, while derived tables contain type-specific properties and use a shared primary key that also serves as a foreign key.

EF Core supports TPT through `UseTptMappingStrategy()` or explicit table mappings. Queries that materialize complete derived entities typically combine data from multiple tables, and polymorphic queries can introduce additional JOINs.

TPT is most useful when an existing schema or specific database requirements justify separate tables. When the mapping strategy is optional, evaluate TPH and TPC against your application's query patterns and performance needs before choosing TPT.

## Related Articles

- [Inheritance in EF Core](/modeling/inheritance) — Compare the three inheritance mapping strategies and their database structures.
- [Table Per Hierarchy (TPH) in EF Core](/modeling/inheritance-table-per-hierarchy) — Learn how EF Core's default strategy stores an inheritance hierarchy in one table using a discriminator.
- [Table Per Concrete Type (TPC) in EF Core](/modeling/inheritance-table-per-concrete-type) — Explore how concrete types are stored in separate tables without requiring a shared base table.
