---
title: Inheritance in EF Core — TPH, TPT, and TPC Compared
description: "Compare inheritance mapping strategies in Entity Framework Core: Table Per Hierarchy (TPH), Table Per Type (TPT), and Table Per Concrete Type (TPC)."
canonical: /modeling/inheritance
status: Published
lastmod: 2026-10-13
---

# Inheritance in EF Core

EF Core supports three ways to map an inheritance hierarchy to relational tables: Table Per Hierarchy (TPH), Table Per Type (TPT), and Table Per Concrete Type (TPC). The main difference is **where each entity's inherited and type-specific properties are stored**, which affects queries and database constraints.

If you do not have a specific database design requirement, start with [Table Per Hierarchy (TPH)](/modeling/inheritance-table-per-hierarchy), EF Core's default strategy.

Jump to a mapping strategy:

- [Table Per Hierarchy (TPH)](#table-per-hierarchy-tph)
- [Table Per Type (TPT)](#table-per-type-tpt)
- [Table Per Concrete Type (TPC)](#table-per-concrete-type-tpc)

The following `Animal`, `Cat`, and `Dog` model is also used in the dedicated inheritance articles:

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

The CLR hierarchy is the same in all three strategies. **The database representation changes.** The illustrations below show those differences; the dedicated articles provide full configuration, schema, and SQL examples.

## Quick Comparison

| Strategy | Database structure | Querying a complete derived entity | Querying across the hierarchy |
| --- | --- | --- | --- |
| **TPH** | One shared table, with a stored discriminator | Reads the shared table, filtering by type | Reads the shared table; no inheritance JOINs |
| **TPT** | Base and derived tables connected by shared primary/foreign keys | Typically combines base and derived tables using JOINs | May JOIN several derived tables |
| **TPC** | One table per concrete type, repeating inherited columns | Reads the concrete type's table without inheritance JOINs | Typically combines concrete tables using `UNION ALL` |

These are typical query shapes for the hierarchy itself, not guarantees about every LINQ query. Relationships, projections, and database providers can change the generated SQL.

## Choosing an Inheritance Mapping Strategy

- **Start with TPH** when you can choose the schema freely, especially if you often query different types together. Its single-table design avoids joins between inheritance tables, but derived-type properties can create nullable columns.
- **Consider TPT** when you must work with an existing base-and-derived-table schema or have a specific requirement to keep inherited and type-specific properties in separate related tables. Materializing complete entities can require additional JOINs.
- **Evaluate TPC** when most queries target one concrete type at a time and separate, self-contained tables suit the application. Account for duplicated inherited columns, hierarchy-wide key uniqueness, and limitations on database foreign keys targeting the entire hierarchy.

There is no universally fastest strategy. Compare the queries your application actually runs, the resulting schema, and measured performance before making a final choice.

## How the Three Strategies Map the Same Hierarchy

### Table Per Hierarchy (TPH)

<div class="image-outer"><img src="/images/efcore/inheritance/table-per-hierarchy.png" loading="lazy" alt="TPH schema with one Animals table and a discriminator identifying Cat and Dog rows"></div>

TPH stores every entity in a single table. A discriminator identifies each concrete type, while columns used only by some derived types can be `NULL` for other rows. The illustration uses the conventional `Discriminator` column; the dedicated article also demonstrates a custom integer discriminator.

[Online Example](https://dotnetfiddle.net/SitJWp) · [Explore TPH configuration and queries](/modeling/inheritance-table-per-hierarchy)

### Table Per Type (TPT)

<div class="image-outer"><img src="/images/efcore/inheritance/table-per-type.png" loading="lazy" alt="TPT schema with Animals base table and related Cats and Dogs tables"></div>

TPT stores inherited properties in a base table and type-specific properties in derived tables. Each derived row shares the entity's primary key with the corresponding base row, so complete derived entities typically require data from both tables.

[Online Schema Example](https://dotnetfiddle.net/3nzgSS) · [Explore TPT configuration and JOINs](/modeling/inheritance-table-per-type)

### Table Per Concrete Type (TPC)

<div class="image-outer"><img src="/images/efcore/inheritance/table-per-concrete-type.png" loading="lazy" alt="TPC schema with separate Cats and Dogs tables, each containing inherited properties"></div>

TPC gives each concrete type its own table containing both inherited and type-specific properties. With an abstract `Animal` base class, there is no `Animals` table. Queries that span different concrete types typically use `UNION ALL`; a query restricted to one concrete type can read its table directly.

[Online Example](https://dotnetfiddle.net/FusUAZ) · [Explore TPC keys, SQL, and limitations](/modeling/inheritance-table-per-concrete-type)

## Related Articles

- [Table Per Hierarchy (TPH)](/modeling/inheritance-table-per-hierarchy) — Default strategy, discriminators, and single-table queries.
- [Table Per Type (TPT)](/modeling/inheritance-table-per-type) — Shared keys, related tables, and JOIN behavior.
- [Table Per Concrete Type (TPC)](/modeling/inheritance-table-per-concrete-type) — Concrete tables, key generation, `UNION ALL`, and referential-integrity considerations.
