---
title: Delete Behavior in EF Core
description: Learn how to configure delete behavior for one-to-many, one-to-one, and many-to-many relationships in EF Core using OnDelete and Fluent API.
canonical: /modeling/relationships-delete-behavior
status: Published
lastmod: 2026-11-23
---

# EF Core Delete Behavior

In EF Core, delete behavior determines what happens to related entities when an entity is deleted. Required relationships use cascade delete by default, but you can change this behavior with `OnDelete()`. For a detailed explanation, see [Cascade Delete in EF Core](/saving/cascade-delete).

## One-to-Many Delete Behavior

To prevent automatic deletion of an author's books, configure `DeleteBehavior.Restrict`:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Author>()
        .HasMany(a => a.Books)
        .WithOne(b => b.Author)
        .OnDelete(DeleteBehavior.Restrict);
}
```

This configuration belongs in the `OnModelCreating()` method of your `DbContext` and uses the following entities:

```csharp
public class Author
{
    public int AuthorId { get; set; }
    public ICollection<Book> Books { get; set; } = new List<Book>();
}

public class Book
{
    public int BookId { get; set; }
    public int AuthorId { get; set; }
    public Author Author { get; set; } = null!;
}
```

The non-nullable `AuthorId` foreign key makes this a required relationship. The Fluent API configuration overrides the default cascade delete behavior to prevent automatic deletion of the related books.

The configuration works as follows:

- **`HasMany(a => a.Books)`** identifies the collection of books associated with an author.
- **`WithOne(b => b.Author)`** identifies the reference navigation from each book to its author.
- **`OnDelete(DeleteBehavior.Restrict)`** disables cascade deletion of the related books.

With this configuration, deleting an author while books still reference that author causes the operation to fail unless those relationships are resolved first. Because the relationship is required, the books cannot simply have their `AuthorId` set to `null`.

For more about defining required and optional relationships, see [One-to-Many Relationships in EF Core](/modeling/relationships-one-to-many).

## One-to-One Delete Behavior

In a one-to-one relationship, an author can have one biography, and each biography belongs to one author. To prevent automatic deletion of the biography, configure `DeleteBehavior.Restrict`:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Author>()
        .HasOne(a => a.Biography)
        .WithOne(b => b.Author)
        .OnDelete(DeleteBehavior.Restrict);
}
```

This example uses the following entities:

```csharp
public class Author
{
    public int AuthorId { get; set; }
    public AuthorBiography? Biography { get; set; }
}

public class AuthorBiography
{
    public int AuthorBiographyId { get; set; }
    public int AuthorId { get; set; }
    public Author Author { get; set; } = null!;
}
```

The non-nullable `AuthorBiography.AuthorId` foreign key makes this relationship required.

Unlike the previous one-to-many example, **`HasOne()` and `WithOne()`** configure a relationship with a single dependent entity. The `OnDelete(DeleteBehavior.Restrict)` call prevents cascade deletion of the biography.

If an author still has a related biography, the relationship must be resolved before the author can be deleted successfully.

For more about this relationship and its foreign key configurations, see [One-to-One Relationships in EF Core](/modeling/relationships-one-to-one).

## Many-to-Many Delete Behavior

In a many-to-many relationship, a book can belong to multiple categories, and each category can contain multiple books. EF Core uses a join table to store these associations.

By default, deleting a book or category also deletes its related rows in the join table. To prevent this automatic deletion, configure `DeleteBehavior.Restrict` for both relationships in `OnModelCreating()`:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Book>()
        .HasMany(b => b.Categories)
        .WithMany(c => c.Books)
        .UsingEntity(
            r => r.HasOne(typeof(Category))
                  .WithMany()
                  .OnDelete(DeleteBehavior.Restrict),
            l => l.HasOne(typeof(Book))
                  .WithMany()
                  .OnDelete(DeleteBehavior.Restrict));
}
```

This configuration uses the following entities:

```csharp
public class Book
{
    public int BookId { get; set; }
    public ICollection<Category> Categories { get; set; } = new List<Category>();
}

public class Category
{
    public int CategoryId { get; set; }
    public ICollection<Book> Books { get; set; } = new List<Book>();
}
```

Unlike the previous examples, a many-to-many relationship uses a join table, named `BookCategory` by convention, with foreign keys referencing both `Book` and `Category`.

The configuration works as follows:

- **`HasMany()` and `WithMany()`** identify the many-to-many relationship between books and categories.
- **`UsingEntity()`** allows you to configure the relationships connecting the join table to both entities.
- **The first `OnDelete(DeleteBehavior.Restrict)`** prevents cascade deletion of join table rows when a related category is deleted.
- **The second `OnDelete(DeleteBehavior.Restrict)`** applies the same behavior when a related book is deleted.

With this configuration, deleting a book or category while its join table rows still reference it can fail because of the foreign key constraints. To delete the entity successfully, those associations must first be removed or otherwise resolved.

This does not prevent you from explicitly removing an association between a book and a category. It prevents automatic cascade deletion of join rows when their related book or category is deleted.

You do not need to define a separate `BookCategory` class for this configuration. EF Core can manage the join entity automatically.

For more about join entities and their configuration, see [Many-to-Many Relationships in EF Core](/modeling/relationships-many-to-many).

## Important Considerations

Before changing the default delete behavior, keep these points in mind:

- **Tracked entities:** EF Core may detect an invalid required relationship before sending changes to the database. If the related entities are not loaded, the database may reject the deletion instead.
- **Database providers:** The generated foreign key constraints can differ by provider. For example, SQLite supports `ON DELETE RESTRICT`, while SQL Server uses `ON DELETE NO ACTION`.

For a detailed explanation of these behaviors and their effects on `SaveChanges()`, see [Cascade Delete in EF Core](/saving/cascade-delete).

## External Resources - Delete Behavior

The following videos complement the relationship mapping examples in this article with explanations and practical demonstrations of delete behavior in EF Core. They cover the differences between `Cascade` and `Restrict`, configuration through Fluent API, and how delete behavior affects database foreign key constraints.

### Video 1 - Day 36/100 | DeleteBehavior in EF Core Explained | Cascade vs Restrict | Real Example (2026)

<iframe width="560" height="315" src="https://www.youtube.com/embed/3BqsIa_V0e0" title="Day 36/100 | DeleteBehavior in EF Core Explained | Cascade vs Restrict | Real Example (2026)" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

This short video from CodeWithIndu introduces `DeleteBehavior.Cascade` and `DeleteBehavior.Restrict` using simple parent-child relationships. It explains how these behaviors affect related records and illustrates cascade deletion with a one-to-one `User` and `UserProfile` example.

**Key timestamps:**

- **[00:16](https://www.youtube.com/watch?v=3BqsIa_V0e0&t=16s)** — Introduces delete behavior and the relationship between principal and dependent entities.
- **[00:55](https://www.youtube.com/watch?v=3BqsIa_V0e0&t=55s)** — Explains cascade deletion and its default use in required relationships.
- **[01:23](https://www.youtube.com/watch?v=3BqsIa_V0e0&t=83s)** — Uses `User` and `UserProfile` to illustrate cascade deletion in a one-to-one relationship.
- **[02:12](https://www.youtube.com/watch?v=3BqsIa_V0e0&t=132s)** — Introduces `Restrict` and explains why deleting a principal with existing dependents can fail.

The video provides a quick conceptual introduction before moving into more detailed relationship configurations. It illustrates the behaviors rather than demonstrating a complete database execution.

### Video 2 - C# Entity Framework - Delete Behaviors

<iframe width="560" height="315" src="https://www.youtube.com/embed/_3QiR07dfL4" title="C# Entity Framework - Delete Behaviors" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

In this lecture, Rainer Stropek demonstrates delete behavior using a required one-to-many relationship between `OrderHeader` and `OrderDetail`. He configures the relationship through Fluent API and examines what happens when a principal entity is deleted while related records still exist.

**Key timestamps:**

- **[09:31](https://www.youtube.com/watch?v=_3QiR07dfL4&t=571s)** — Explains the referential integrity problem when deleting an order header with related order details.
- **[12:23](https://www.youtube.com/watch?v=_3QiR07dfL4&t=743s)** — Introduces `OnModelCreating()` and Fluent API for configuring relationships.
- **[16:58](https://www.youtube.com/watch?v=_3QiR07dfL4&t=1018s)** — Demonstrates `OnDelete()` and discusses the available delete behavior options.
- **[20:03](https://www.youtube.com/watch?v=_3QiR07dfL4&t=1203s)** — Shows a failed deletion when `NoAction` is configured and dependent records still reference the principal.

The example uses `DeleteBehavior.NoAction` rather than `Restrict`, as in this article. It is especially useful for seeing how a relationship configuration affects actual database operations and foreign key constraints.

### Video 3 - Enforce ON DELETE NO ACTION in entity framework core

<iframe width="560" height="315" src="https://www.youtube.com/embed/txTZAFut9mA" title="Enforce ON DELETE NO ACTION in entity framework core" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

This video from kudvenkat demonstrates how to prevent automatic cascade deletion in an ASP.NET Core Identity application. Using `AspNetUsers`, `AspNetRoles`, and the `AspNetUserRoles` join table, it shows how changing delete behavior affects foreign key constraints and how to verify the result in SQL Server.

**Key timestamps:**

- **[02:23](https://www.youtube.com/watch?v=txTZAFut9mA&t=143s)** — Introduces the Identity tables and the join table connecting users and roles.
- **[07:03](https://www.youtube.com/watch?v=txTZAFut9mA&t=423s)** — Shows how to change delete behavior in `OnModelCreating()`.
- **[07:43](https://www.youtube.com/watch?v=txTZAFut9mA&t=463s)** — Generates a migration to update the foreign key constraints.
- **[09:27](https://www.youtube.com/watch?v=txTZAFut9mA&t=567s)** — Demonstrates that deleting a role with associated users fails because of a foreign key constraint.

Unlike the relationship-specific examples in this article, the video applies `Restrict` to all foreign keys in the model. This broader approach illustrates database-level constraints but does not demonstrate individual relationship configuration with `OnDelete()`.

## Summary

Use `OnDelete()` in `OnModelCreating()` to configure how EF Core handles relationship deletions. In these examples, `DeleteBehavior.Restrict` prevents automatic cascade deletion, so existing dependent relationships must be resolved before the principal entity can be deleted.

- **One-to-Many:** Configure the relationship with `HasMany()` and `WithOne()`.
- **One-to-One:** Configure the relationship with `HasOne()` and `WithOne()`.
- **Many-to-Many:** Use `UsingEntity()` to configure delete behavior for both foreign keys of the join entity.

## Related Articles

Continue exploring how EF Core maps and configures relationships:

- [One-to-Many Relationships in EF Core](/modeling/relationships-one-to-many) — Learn how to configure one-to-many relationships, including navigation properties and foreign keys.
- [One-to-One Relationships in EF Core](/modeling/relationships-one-to-one) — Explore required and optional one-to-one relationships and their foreign key configurations.
- [Many-to-Many Relationships in EF Core](/modeling/relationships-many-to-many) — Learn how EF Core maps many-to-many relationships and manages join entities.

## FAQ

### Does EF Core delete related entities by default?

It depends on the relationship. Required relationships use `DeleteBehavior.Cascade` by convention, while optional relationships use `DeleteBehavior.ClientSetNull` by default.

### Can I use a different DeleteBehavior value instead of Restrict?

Yes. You can pass another `DeleteBehavior` value to `OnDelete()`, depending on the behavior you need. Some values perform cascading actions, while others prevent them or affect only tracked entities.

### Does DeleteBehavior.Restrict affect both EF Core and the database?

Yes. `DeleteBehavior.Restrict` influences how EF Core handles tracked relationships and how foreign key constraints are created in the database. The exact behavior can depend on whether related entities are loaded and on the database provider.

### Do I need an explicit join entity to configure delete behavior in Many-to-Many?

No. You can use `UsingEntity()` to configure delete behavior for both foreign keys without defining a separate join entity class.