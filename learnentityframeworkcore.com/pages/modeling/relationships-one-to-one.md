---
title: Entity Framework Core One-To-One Relationships Conventions
description: How to design and configure one-to-one relationships in Entity Framework Core using conventions, shadow foreign keys, and required or optional relationships.
canonical: /modeling/relationships-one-to-one
status: Published
lastmod: 2026-11-23
---

# One-to-One Relationships in EF Core

A one-to-one relationship allows each entity to be associated with at most one entity of another type. For example, an author can have one biography, and each biography belongs to one author. In EF Core, you can configure this relationship as required or optional, using a shadow or an explicit foreign key. For advanced configurations, see the [official EF Core documentation](https://learn.microsoft.com/en-us/ef/core/modeling/relationships/one-to-one).

## Basic One-to-One Relationship — Required with a Shadow Foreign Key
```csharp
public class Author
{
    public int AuthorId { get; set; }
    public AuthorBiography? Biography { get; set; }
}

public class AuthorBiography
{
    public int AuthorBiographyId { get; set; }
    public Author Author { get; set; } = null!;
}
```

Configure the relationship in `OnModelCreating`:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Author>()
        .HasOne(a => a.Biography)
        .WithOne(b => b.Author)
        .HasForeignKey<AuthorBiography>("AuthorId")
        .IsRequired();
}
```

This example follows EF Core's [required one-to-one configuration with a shadow foreign key](https://learn.microsoft.com/en-us/ef/core/modeling/relationships/one-to-one#required-one-to-one-with-shadow-foreign-key).

In this configuration:

- **`Author`** is the principal entity, and **`AuthorBiography`** is the dependent entity.
- **`HasOne()` and `WithOne()`** define the reference navigations between the two entities.
- **`HasForeignKey<AuthorBiography>("AuthorId")`** configures a shadow foreign key named `AuthorId`. EF Core maintains this property in its model even though it is not declared in the `AuthorBiography` class.
- **`IsRequired()`** makes the relationship required, so every biography must reference an author.

With a relational database provider, EF Core typically creates a unique index on the foreign key to prevent multiple biographies from referencing the same author.

However, a required one-to-one relationship does not mean every author must have a biography.

## Optional One-to-One Relationship

The previous example requires every `AuthorBiography` to reference an `Author`. To make this relationship optional, the dependent entity must be allowed to exist without a principal.

The main difference is that the navigation from `AuthorBiography` to `Author` is nullable:

```csharp
public class Author
{
    public int AuthorId { get; set; }
    public AuthorBiography? Biography { get; set; }
}

public class AuthorBiography
{
    public int AuthorBiographyId { get; set; }
    public Author? Author { get; set; }
}
```

Configure the relationship as optional in `OnModelCreating`:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Author>()
        .HasOne(a => a.Biography)
        .WithOne(b => b.Author)
        .HasForeignKey<AuthorBiography>("AuthorId")
        .IsRequired(false);
}
```

The `IsRequired(false)` method makes the shadow foreign key nullable, allowing an `AuthorBiography` to exist without an associated `Author`.

When a biography references an author, the one-to-one uniqueness constraint still applies, so an author cannot have multiple biographies.

For more details, see the [optional one-to-one relationship with a shadow foreign key](https://learn.microsoft.com/en-us/ef/core/modeling/relationships/one-to-one#optional-one-to-one-with-shadow-foreign-key) in the official EF Core documentation.

## One-to-One with an Explicit Foreign Key

Both previous examples use a shadow foreign key. You can also declare the foreign key as a regular property in the dependent entity, allowing EF Core to discover the relationship by convention when the model follows its naming conventions.

The following example defines a one-to-one relationship between `Author` and `AuthorBiography` using an explicit `AuthorId` property:

```csharp
public class Author
{
    public int AuthorId { get; set; }
    public string FirstName { get; set; }
    public string LastName { get; set; }
    public AuthorBiography Biography { get; set; }
}

public class AuthorBiography
{
    public int AuthorBiographyId { get; set; }
    public string Biography { get; set; }
    public DateTime DateOfBirth { get; set; }
    public string PlaceOfBirth { get; set; }
    public string Nationality { get; set; }
    public int AuthorId { get; set; }
    public Author Author { get; set; }
}
```

In this example, EF Core discovers the one-to-one relationship by convention because both entities have reference navigation properties, and `AuthorBiography.AuthorId` references the primary key `Author.AuthorId`. Since the foreign key is a non-nullable `int`, the relationship is required.

EF Core also creates a unique index on the foreign key, ensuring that each author can be associated with at most one biography.

Unlike the previous examples, `AuthorId` is declared directly in the `AuthorBiography` class instead of being maintained as a shadow property.

The choice between a shadow and an explicit foreign key is separate from whether the relationship is required or optional.

If the foreign key uses a non-conventional name, such as `AuthorRef` instead of `AuthorId`, EF Core may not identify the dependent entity from the two reference navigations alone. In that case, replace the `public int AuthorId { get; set; }` property in `AuthorBiography` with `public int AuthorRef { get; set; }`. Then, configure the relationship explicitly in `OnModelCreating`:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Author>()
        .HasOne(a => a.Biography)
        .WithOne(b => b.Author)
        .HasForeignKey<AuthorBiography>(b => b.AuthorRef);
}
```

This identifies `AuthorBiography` as the dependent and maps its existing `AuthorRef` property as the foreign key. The relationship remains required because `AuthorRef` is a non-nullable `int`.

## Other One-to-One Configurations

EF Core supports other one-to-one configurations for more specialized models. The following table links to the relevant official documentation.

| Configuration | When to use it |
|---|---|
| [Primary Key to Primary Key](https://learn.microsoft.com/en-us/ef/core/modeling/relationships/one-to-one#required-one-to-one-with-primary-key-to-primary-key-relationship) | When the dependent entity uses its primary key as the foreign key. |
| [Without Navigation Properties](https://learn.microsoft.com/en-us/ef/core/modeling/relationships/one-to-one#one-to-one-with-no-navigations) | When neither entity exposes a navigation property and the relationship must be configured explicitly. |
| [Alternate Key](https://learn.microsoft.com/en-us/ef/core/modeling/relationships/one-to-one#one-to-one-with-alternate-key) | When the foreign key references an alternate key instead of the principal's primary key. |
| [Composite Foreign Key](https://learn.microsoft.com/en-us/ef/core/modeling/relationships/one-to-one#one-to-one-with-composite-foreign-key) | When the relationship uses multiple foreign key properties. |
| [Self-Referencing One-to-One](https://learn.microsoft.com/en-us/ef/core/modeling/relationships/one-to-one#self-referencing-one-to-one) | When an entity has a one-to-one relationship with another instance of the same type. |
| [Table Splitting](https://learn.microsoft.com/en-us/ef/core/modeling/table-splitting#table-splitting) | When two entity types share a single database table and row; this is an advanced table-mapping scenario. |

Required relationships use cascade delete by convention. To configure deletion behavior, see [Cascade Delete in EF Core](/saving/cascade-delete).

## External Resources

The following videos complement the examples in this article with practical EF Core configuration, a closer look at shadow foreign keys, and visual explanations of optional and required relationships.

### Video 1 - Master EF Core One-to-One Relationships in 10 Minutes — Poorna Soysa

<iframe width="560" height="315" src="https://www.youtube.com/embed/5pVWefeTuvY" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

This tutorial demonstrates a one-to-one relationship between `Customer` and `CustomerProfile`, covering Fluent API configuration, foreign keys, migrations, and database inspection. It also shows how to resolve a foreign key type mismatch.

**Key timestamps:**

- [01:28 — Identifying principal and dependent entities](https://www.youtube.com/watch?v=5pVWefeTuvY&t=88s)
- [03:03 — Configuring the relationship with Fluent API](https://www.youtube.com/watch?v=5pVWefeTuvY&t=183s)
- [05:12 — Fixing a foreign key type mismatch](https://www.youtube.com/watch?v=5pVWefeTuvY&t=312s)
- [06:30 — Inspecting the generated database table](https://www.youtube.com/watch?v=5pVWefeTuvY&t=390s)

### Video 2 - Shadow Properties - EF Core - Foreign Keys — Full Stack Amigo

<iframe width="560" height="315" src="https://www.youtube.com/embed/SSqB_Z48co4" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

This video explains how EF Core manages foreign keys that are not declared in entity classes. Using `User` and `Tweet`, it demonstrates shadow properties, how to access their values, and how to configure a required shadow foreign key.

**Key timestamps:**

- [01:26 — Inspecting the generated shadow foreign key column](https://www.youtube.com/watch?v=SSqB_Z48co4&t=86s)
- [02:00 — Understanding shadow properties and the Change Tracker](https://www.youtube.com/watch?v=SSqB_Z48co4&t=120s)
- [02:42 — Accessing and querying shadow properties](https://www.youtube.com/watch?v=SSqB_Z48co4&t=162s)
- [04:44 — Configuring a required shadow foreign key](https://www.youtube.com/watch?v=SSqB_Z48co4&t=284s)

Although the demonstration uses a one-to-many relationship, the shadow foreign key concepts also apply to the one-to-one configurations discussed in this article. Foreign key requiredness depends on the relationship configuration and applicable nullability conventions.

### Video 3 - ERD: Relationship Cardinalities — Dustin Ormond

<iframe width="560" height="315" src="https://www.youtube.com/embed/9dtSEavLIWA" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

This video uses entity-relationship diagrams to explain optional and mandatory participation. Its one-to-one examples illustrate the difference between zero-or-one and exactly-one associations, helping clarify how required and optional relationships differ conceptually.

**Key timestamps:**

- [00:16 — Understanding optional one (zero or one)](https://www.youtube.com/watch?v=9dtSEavLIWA&t=16s)
- [00:47 — Understanding mandatory one (exactly one)](https://www.youtube.com/watch?v=9dtSEavLIWA&t=47s)
- [01:33 — Optional one-to-one relationship example](https://www.youtube.com/watch?v=9dtSEavLIWA&t=93s)
- [02:32 — Optional and mandatory sides of a one-to-one relationship](https://www.youtube.com/watch?v=9dtSEavLIWA&t=152s)

This is a conceptual database-modeling resource rather than an EF Core tutorial. It helps explain why a required one-to-one relationship does not necessarily mean every principal entity must have a dependent entity.

## Summary

Choose the one-to-one configuration based on whether the foreign key should be nullable and whether it is declared in your entity class.

- Use **`HasForeignKey<TDependent>("ForeignKeyName")`** with **`IsRequired()`** for a required relationship with a shadow foreign key.
- Use **`IsRequired(false)`** for an optional relationship with a shadow foreign key, allowing the dependent entity to exist without a principal.
- Use an **explicit foreign key property** when you want the foreign key directly in your entity class. EF Core can discover the relationship by convention when the model follows its conventions.

For other relationship patterns, use the official documentation links above.

## Related Articles

- [One-to-Many Relationships in EF Core](/modeling/relationships-one-to-many)
- [Many-to-Many Relationships in EF Core](/modeling/relationships-many-to-many)

## FAQ

### Do both entities need navigation properties for a one-to-one relationship?

No. EF Core supports one-to-one relationships with a navigation on only one side or with no navigation properties. Depending on the available navigations and foreign key properties, explicit Fluent API configuration may be necessary.

### What if EF Core cannot determine the dependent entity?

EF Core needs to identify which entity holds the foreign key. If it cannot determine the dependent entity by convention, use `HasForeignKey<TDependent>()` to identify it and configure the relationship explicitly.

### How can I access a shadow foreign key?

Use `EF.Property<T>()` to access a shadow foreign key in LINQ queries, or `context.Entry(entity).Property()` to read or modify its value on a tracked entity. The property exists in the EF Core model even though it is not declared in the entity class.

### Can a one-to-one relationship be optional on one side and required on the other?

Yes. In a required one-to-one relationship, every dependent must reference a principal, but a principal can still exist without a dependent. For example, every `AuthorBiography` must belong to an `Author`, while an `Author` does not necessarily need a biography.