---
title: Inheritance in Entity Framework Core
description: Learn how inheritance works in Entity Framework Core and explore the three supported mapping strategies: Table Per Hierarchy (TPH), Table Per Type (TPT), and Table Per Concrete Type (TPC).
canonical: /inheritance
status: Published
lastmod: 2026-02-21
---

# EF Core Inheritance

Inheritance is a fundamental object-oriented programming concept used to model hierarchical relationships and promote code reuse.

In Entity Framework Core, inheritance hierarchies can be mapped to a relational database using three different strategies:

- [Table Per Hierarchy (TPH)](/inheritance#table-per-hierarchy-tph)
- [Table Per Type (TPT)](/inheritance#table-per-type-tpt)
- [Table Per Concrete Type (TPC)](/inheritance#table-per-concrete-type-tpc)

Consider the following simple hierarchy:

```csharp
public abstract class Animal
{
    public int Id { get; set; }
    public string Name { get; set; }
}

public class Cat : Animal
{
    public int Lives { get; set; }
}

public class Dog : Animal
{
    public bool IsGoodBoy { get; set; }
}
````

The base type is `Animal`.
`Cat` and `Dog` inherit common properties from the base class and define additional type-specific properties.

The key question is how this hierarchy should be represented in a relational database.

Entity Framework Core supports three inheritance mapping strategies (TPH, TPT, TPC).

## Table Per Hierarchy (TPH)

<div class="image-outer"><img src="/images/efcore/inheritance/table-per-hierarchy.png" loading="lazy" alt="EF Core TPH Inheritance"></div>

**Table Per Hierarchy (TPH)** maps the entire inheritance hierarchy to a single database table.

A discriminator column is used to distinguish between different derived types.

* One table stores all types in the hierarchy
* No JOINs are required when querying derived types
* Queries are typically simpler

This strategy is commonly selected when query performance and simplicity are priorities.

[Learn more about Table Per Hierarchy →](/inheritance/table-per-hierarchy)

## Table Per Type (TPT)

<div class="image-outer"><img src="/images/efcore/inheritance/table-per-type.png" loading="lazy" alt="EF Core TPT Inheritance"></div>

**Table Per Type (TPT)** maps each type in the hierarchy to its own database table.

The base table stores shared properties, while each derived table stores only properties specific to that type. Derived tables are linked to the base table using foreign keys.

* Multiple related tables
* JOIN operations are required to reconstruct derived types
* Produces a fully normalized schema

This strategy is often chosen when database normalization is more important than raw query performance.

[Learn more about Table Per Type →](/inheritance/table-per-type)

## Table Per Concrete Type (TPC)

<div class="image-outer"><img src="/images/efcore/inheritance/table-per-concrete-type.png" loading="lazy" alt="EF Core TPC Inheritance"></div>

**Table Per Concrete Type (TPC)** maps each concrete type to its own table.

Each table includes both the base properties and the properties specific to that concrete type. There is no shared base table.

* Separate tables for each concrete type
* No discriminator column
* Polymorphic queries are translated using `UNION ALL`

This strategy can be appropriate when avoiding JOINs is desirable and duplication of base columns is acceptable.

[Learn more about Table Per Concrete Type →](/inheritance/table-per-concrete)

## Quick Comparison

| Strategy | Tables                | Query Shape   | Schema Style          | Typical Characteristics  |
| -------- | --------------------- | ------------- | --------------------- | ------------------------ |
| TPH      | Single table          | Simple SELECT | Denormalized          | Simpler queries, no JOIN |
| TPT      | Base + derived tables | JOIN          | Normalized            | Clear schema separation  |
| TPC      | Concrete tables only  | UNION ALL     | Denormalized per type | No shared base table     |

## Choosing the Right Strategy

There is no universally optimal inheritance strategy. The appropriate choice depends on:

* Expected query patterns
* Performance requirements
* Schema design constraints
* Reporting and maintenance considerations

In practice:

* **TPH** is frequently preferred for simplicity and efficient querying
* **TPT** is used when strict normalization is required
* **TPC** may be suitable when JOINs must be avoided and duplication is acceptable

Each strategy involves trade-offs. Reviewing the dedicated documentation for each pattern will help determine which approach best fits your domain model.

## Further Reading

* [Table Per Hierarchy](/inheritance/table-per-hierarchy)
* [Table Per Type](/inheritance/table-per-type)
* [Table Per Concrete Type](/inheritance/table-per-concrete)