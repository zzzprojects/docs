---
title: Self-Referencing Relationships in EF Core
description: Learn how to configure self-referencing relationships in EF Core, including one-to-many employee hierarchies and one-to-one and many-to-many examples.
canonical: /modeling/relationships-self-reference
status: Published
lastmod: 2026-11-23
---

# Self-Referencing Relationships in EF Core

A self-referencing relationship in EF Core allows records of the same entity type to be related to each other. This is useful for representing hierarchies, such as employees and their managers within a company.

For example, an employee can have one manager and supervise multiple employees. This creates a [one-to-many relationship](/modeling/relationships-one-to-many) within the same entity. For other configurations, see the [official EF Core documentation](https://learn.microsoft.com/en-us/ef/core/modeling/relationships/one-to-many#self-referencing-one-to-many).

## Self-Referencing One-to-Many Relationships

```csharp
public class Employee
{
    public int Id { get; set; }

    public int? ManagerId { get; set; }
    public Employee? Manager { get; set; }

    public ICollection<Employee> Reports { get; set; } = new List<Employee>();
}
```

This example represents an organizational hierarchy where each employee can report to another employee and supervise multiple employees.

- `Id` is the primary key identifying an employee.
- `ManagerId` is a nullable foreign key referencing another employee's `Id`.
- `Manager` is a reference navigation pointing to the employee's manager.
- `Reports` is a collection navigation containing the employees who report directly to that employee.

EF Core can discover this self-referencing one-to-many relationship by convention because the foreign key and navigation properties follow its relationship conventions.

The nullable `ManagerId` allows an employee at the top of the hierarchy to have no manager.

Although both ends of the relationship use `Employee`, the cardinality remains one-to-many: one manager can have multiple direct reports, while each employee has at most one direct manager.

## Configuring a Self-Referencing One-to-Many Relationship

You can also configure the relationship explicitly using the Fluent API.

Inside your `DbContext`, add the following configuration to `OnModelCreating`:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Employee>()
        .HasOne(e => e.Manager)
        .WithMany(e => e.Reports)
        .HasForeignKey(e => e.ManagerId)
        .IsRequired(false);
}
```

The configuration specifies that:

- `HasOne(e => e.Manager)` identifies the reference navigation to the manager.
- `WithMany(e => e.Reports)` identifies the collection navigation containing direct reports.
- `HasForeignKey(e => e.ManagerId)` uses `ManagerId` as the foreign key.
- `IsRequired(false)` makes the relationship optional.

The Fluent API makes the relationship explicit, but it is not required when EF Core can identify the same relationship by convention.

For more information about configuring ordinary one-to-many relationships, see [One-to-Many Relationship Configuration](/configuration/one-to-many-relationship-configuration).

### How the Employee Hierarchy Works

Suppose the `Employees` table contains these records:

| Id | Employee | ManagerId |
|---|---|---|
| 1 | Alice | NULL |
| 2 | Bob | 1 |
| 3 | Carol | 2 |

These records form the following hierarchy:

```text
Alice
└── Bob
    └── Carol
```

Alice has no manager because her `ManagerId` is `NULL`.

Bob reports directly to Alice, and Carol reports directly to Bob.

For Alice, `Reports` identifies Bob as a direct report, not Carol as an indirect report.

Similarly, Bob is Carol's manager, while Alice is Carol's manager's manager.

**A self-referencing relationship defines how records are related; it does not automatically load the entire hierarchy.**

When querying employees, you can use `Include()` and `ThenInclude()` to request specific levels of related data. See [Eager Loading in EF Core](/querying/eager-loading) for more information about loading related entities.

## Self-Referencing One-to-One Relationships

A self-referencing relationship can also be one-to-one, where each record can participate in a specific role at most once. For example, a `Person` entity can represent an optional relationship between two people.

```csharp
public class Person
{
    public int Id { get; set; }

    public int? HusbandId { get; set; }
    public Person? Husband { get; set; }
    public Person? Wife { get; set; }
}
```

EF Core can discover this relationship by convention. However, because both sides use the same entity type, the following Fluent API configuration makes the relationship direction explicit:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Person>()
        .HasOne(p => p.Husband)
        .WithOne(p => p.Wife)
        .HasForeignKey<Person>(p => p.HusbandId)
        .IsRequired(false);
}
```

Here, `HusbandId` is the foreign key on the dependent `Person` record. The `Husband` navigation points to the principal, while `Wife` is the inverse navigation.

Because the same entity type appears on both sides, specifying `Person` in `HasForeignKey<Person>()` does not by itself determine which record is the dependent. The navigation configuration establishes that direction.

For relational databases, EF Core typically creates a unique index on the foreign key to enforce the one-to-one relationship.

For a detailed introduction to one-to-one relationships, see [One-to-One Relationships in EF Core](/modeling/relationships-one-to-one) and the [official Microsoft documentation](https://learn.microsoft.com/en-us/ef/core/modeling/relationships/one-to-one#self-referencing-one-to-one).

## Self-Referencing Many-to-Many Relationships

A self-referencing relationship can also be many-to-many.

For example, a person can have multiple parents, and each parent can have multiple children. Both sides of the relationship use the same entity type:

```csharp
public class Person
{
    public int Id { get; set; }

    public List<Person> Parents { get; set; } = new();
    public List<Person> Children { get; set; } = new();
}
```

EF Core can discover this relationship by convention and create a join table called `PersonPerson`, where both foreign keys reference the `People` table.

Unlike the one-to-many example, the many-to-many relationship uses a join table to store associations between records.

This example has two distinct roles: `Parents` and `Children`. A symmetric relationship, such as friends, requires additional consideration and explicit configuration to represent the intended behavior.

For more information, see [Many-to-Many Relationships in EF Core](/modeling/relationships-many-to-many) and the [official Microsoft documentation](https://learn.microsoft.com/en-us/ef/core/modeling/relationships/many-to-many#self-referencing-many-to-many).

## External Resources

The following videos provide complementary perspectives on self-referencing relationships in EF Core. The first demonstrates how to build a parent-child hierarchy using a self-referencing one-to-many relationship, while the second explores how hierarchical data can be represented and queried in more advanced scenarios.

### .NET CORE Entity Framework – Self Referencing Table

<iframe width="560" height="315" src="https://www.youtube.com/embed/OaCZ1LSSkz4" title=".NET CORE Entity Framework – Self Referencing Table" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

This video by Michika Iranga Perera demonstrates how to create a self-referencing one-to-many relationship using a `Node` entity. It covers Fluent API configuration, inserting parent and child records, and building a hierarchical tree from the stored data.

**Key timestamps:**

- [00:00](https://www.youtube.com/watch?v=OaCZ1LSSkz4&t=0s) — Introduces the self-referencing tree structure and parent-child relationships.
- [02:01](https://www.youtube.com/watch?v=OaCZ1LSSkz4&t=121s) — Configures the self-referencing relationship in the `DbContext` using Fluent API.
- [05:41](https://www.youtube.com/watch?v=OaCZ1LSSkz4&t=341s) — Demonstrates creating root and child nodes.
- [09:20](https://www.youtube.com/watch?v=OaCZ1LSSkz4&t=560s) — Builds a hierarchical tree recursively from the stored nodes.

Although the video uses an older version of EF Core, it provides a practical illustration of how records within the same entity type form a hierarchy. The recursive tree-building logic is implemented in application code rather than being performed automatically by EF Core.

### .NET Data Community Standup - Using hierarchical data in SQL Server and PostgreSQL with EF Core

<iframe width="560" height="315" src="https://www.youtube.com/embed/pmnHGWYpCfg" title=".NET Data Community Standup - Using hierarchical data in SQL Server and PostgreSQL with EF Core" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

In this official Microsoft .NET Data Community Standup, Arthur Vickers, Shay Rojansky, and Brice Lambson discuss hierarchical data in EF Core. Alongside SQL Server `HierarchyId` and PostgreSQL `ltree`, they explain the traditional approach of using a foreign key that references the same table and the challenges of querying multiple hierarchy levels.

**Key timestamps:**

- [06:18](https://www.youtube.com/watch?v=pmnHGWYpCfg&t=378s) — Introduces hierarchical tree structures, including roots and child nodes.
- [51:22](https://www.youtube.com/watch?v=pmnHGWYpCfg&t=3082s) — Explains how a `ParentId` foreign key referencing the same table can represent a hierarchy.
- [57:46](https://www.youtube.com/watch?v=pmnHGWYpCfg&t=3466s) — Compares knowing an employee's direct manager with knowing the entire management chain.
- [1:00:02](https://www.youtube.com/watch?v=pmnHGWYpCfg&t=3602s) — Discusses retrieving descendants across multiple levels using recursive queries.

This discussion provides a deeper understanding of the difference between direct parent-child relationships and querying an entire hierarchy. The video was recorded during EF Core 8 development and covers provider-specific features, making it most useful as an optional resource for readers interested in advanced hierarchical data scenarios.

## Summary

Self-referencing relationships allow an EF Core entity type to relate to other records of the same type.

The most common example is a one-to-many hierarchy, such as employees and managers, where a nullable foreign key identifies the parent record and navigation properties provide access to related entities.

EF Core also supports self-referencing one-to-one and many-to-many relationships. The cardinality determines how the relationships are configured, while loading related records remains a separate query concern.

## Related Articles

The following articles explain the relationship types used in these examples and how to load related entities:

- [One-to-Many Relationships in EF Core](/modeling/relationships-one-to-many) — Understand the relationship conventions behind the employee-manager hierarchy.
- [One-to-One Relationships in EF Core](/modeling/relationships-one-to-one) — Learn how EF Core models relationships where each entity is associated with at most one other entity.
- [Many-to-Many Relationships in EF Core](/modeling/relationships-many-to-many) — Explore collection navigations, join tables, and many-to-many relationship conventions.
- [Eager Loading in EF Core](/querying/eager-loading) — Use `Include()` and `ThenInclude()` to load related records when executing a query.

## FAQ

### What is a self-referencing relationship in EF Core?

A self-referencing relationship connects records of the same entity type. For example, an `Employee` entity can reference another `Employee` as its manager.

### Does a self-referencing relationship require Fluent API configuration?

Not always. EF Core can discover many self-referencing relationships by convention when the foreign key and navigation properties provide enough information. Fluent API configuration is useful when you want to make the relationship explicit or customize its behavior.

### Does the foreign key have to be nullable?

No. The foreign key's nullability depends on whether the relationship is optional or required. In the employee hierarchy example, a nullable `ManagerId` allows a top-level employee to exist without a manager.

### Does EF Core automatically load every level of a hierarchy?

No. Defining a self-referencing relationship does not automatically retrieve all ancestors or descendants. You must choose an appropriate loading or querying strategy based on the data your application needs.
