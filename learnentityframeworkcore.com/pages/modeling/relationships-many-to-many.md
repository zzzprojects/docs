---
title: Entity Framework Core Many-To-Many Relationships
description: How to use conventions to establish a many-to-many relationship in Entity Framework Core, or configure it with an explicit join entity.
canonical: /modeling/relationships-many-to-many
status: Published
lastmod: 2026-11-23
---

# Many-to-Many Relationships in EF Core

A many-to-many relationship allows multiple records of one entity to be associated with multiple records of another. For example, a book can belong to several categories, and a category can contain several books. In EF Core 5.0 and later, you can configure this relationship by convention without defining a separate join entity class. For advanced configurations, see the [official EF Core documentation](https://learn.microsoft.com/en-us/ef/core/modeling/relationships/many-to-many).

## Basic Many-to-Many Relationship

```csharp
public class Book
{
    public int BookId { get; set; }
    public string? Title { get; set; }
    public ICollection<Category> Categories { get; set; } = new List<Category>();
}

public class Category
{
    public int CategoryId { get; set; }
    public string? CategoryName { get; set; }
    public ICollection<Book> Books { get; set; } = new List<Book>();
}
```

As you can see, the `Book` class contains a collection of `Categories`, and the `Category` class contains a collection of `Books`. EF Core 5.0 and later recognizes this as a many-to-many relationship by convention, and there is no need for configuration in `OnModelCreating`.

In a relational database, a many-to-many relationship uses a join table to connect the two entities. Each row represents one association between a book and a category, using a foreign key to identify each related record. EF Core manages this table without requiring a separate C# class.

Before EF Core 5.0, many-to-many relationships required an explicit join entity class and Fluent API configuration.

The following `LibraryContext` uses the same entities without adding any relationship configuration:

```csharp
public class LibraryContext : DbContext
{
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
        optionsBuilder.UseSqlServer(@"Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=LibraryDb;Integrated Security=True;");
    }

    public DbSet<Book> Books { get; set; }
    public DbSet<Category> Categories { get; set; }
}
```

When you apply a migration or call the `EnsureCreated` method, EF Core creates the necessary tables, including the join table. The following SQL Server example illustrates the expected table structure; you do not need to write this SQL yourself. The exact generated SQL can vary by EF Core and SQL Server versions.

```sql
CREATE TABLE [dbo].[Books] (
    [BookId]   INT            IDENTITY (1, 1) NOT NULL,
    [Title]    NVARCHAR (MAX) NULL,
    CONSTRAINT [PK_Books] PRIMARY KEY CLUSTERED ([BookId] ASC)
);

CREATE TABLE [dbo].[Categories] (
    [CategoryId]   INT            IDENTITY (1, 1) NOT NULL,
    [CategoryName] NVARCHAR (MAX) NULL,
    CONSTRAINT [PK_Categories] PRIMARY KEY CLUSTERED ([CategoryId] ASC)
);

CREATE TABLE [dbo].[BookCategory] (
    [BooksBookId]          INT NOT NULL,
    [CategoriesCategoryId] INT NOT NULL,
    CONSTRAINT [PK_BookCategory] PRIMARY KEY CLUSTERED ([BooksBookId] ASC, [CategoriesCategoryId] ASC),
    CONSTRAINT [FK_BookCategory_Books_BooksBookId] FOREIGN KEY ([BooksBookId]) REFERENCES [dbo].[Books] ([BookId]) ON DELETE CASCADE,
    CONSTRAINT [FK_BookCategory_Categories_CategoriesCategoryId] FOREIGN KEY ([CategoriesCategoryId]) REFERENCES [dbo].[Categories] ([CategoryId]) ON DELETE CASCADE
);

GO
CREATE NONCLUSTERED INDEX [IX_BookCategory_CategoriesCategoryId]
    ON [dbo].[BookCategory]([CategoriesCategoryId] ASC);
```

EF Core automatically creates a join table named `BookCategory` for this relationship. It contains the foreign keys `BooksBookId` and `CategoriesCategoryId`, which together form a composite primary key that prevents duplicate associations between the same book and category.

## Many-to-Many with a Join Entity

The previous example shows that EF Core creates a join table without requiring a `BookCategory` class. When you need to work with associations directly or store additional data about them, you can define that class explicitly.

Using the same `Book` and `Category` entities, define a `BookCategory` class:

```csharp
public class BookCategory
{
    public int BookId { get; set; }
    public int CategoryId { get; set; }
}
```

Then, add the following configuration to the existing `LibraryContext`:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Book>()
        .HasMany(b => b.Categories)
        .WithMany(c => c.Books)
        .UsingEntity<BookCategory>();
}
```

The `UsingEntity<BookCategory>()` method tells EF Core to use the `BookCategory` class as the join entity. This is the current approach to the explicit join model that older versions of EF Core configured using two `HasOne()` / `WithMany()` relationships and a composite key.

EF Core recognizes `BookId` and `CategoryId` as foreign keys by convention. Together, they form a composite primary key, ensuring that the same book-category association cannot be added twice. No additional key configuration is needed for this example.

This configuration uses `BookId` and `CategoryId` as the join table columns, rather than the convention-generated `BooksBookId` and `CategoriesCategoryId` shown in the basic example.

The `Book.Categories` and `Category.Books` properties are called *skip navigations* because they let you access related books and categories directly, without navigating through the `BookCategory` join entity. You can also extend `BookCategory` with additional properties, such as a creation date, or add navigations to it when you need to access individual association records.

For more advanced configurations, see [Many-to-many with a class for the join entity](https://learn.microsoft.com/en-us/ef/core/modeling/relationships/many-to-many#many-to-many-with-class-for-join-entity).

## Other Many-to-Many Configurations

EF Core supports additional configurations for many-to-many relationships. The following table summarizes common scenarios and links to the corresponding examples in the official documentation.

| Configuration | When to use it | EF Core documentation |
|---|---|---|
| Named join table | Customize the name of the join table. | [Named join table](https://learn.microsoft.com/en-us/ef/core/modeling/relationships/many-to-many#many-to-many-with-named-join-table) |
| Custom foreign key names | Customize the foreign key properties or column names. | [Join table foreign key names](https://learn.microsoft.com/en-us/ef/core/modeling/relationships/many-to-many#many-to-many-with-join-table-foreign-key-names) |
| Navigations to the join entity | Access association records through navigation properties. | [Navigations to join entity](https://learn.microsoft.com/en-us/ef/core/modeling/relationships/many-to-many#many-to-many-with-navigations-to-join-entity) |
| Join entity with payload | Store additional information about an association, such as its creation date. | [Join table with payload](https://learn.microsoft.com/en-us/ef/core/modeling/relationships/many-to-many#many-to-many-and-join-table-with-payload) |
| Unidirectional many-to-many | Configure a relationship when only one entity has a collection navigation. | [Unidirectional many-to-many](https://learn.microsoft.com/en-us/ef/core/modeling/relationships/many-to-many#unidirectional-many-to-many) |

## External Resources

The following videos complement the examples in this article with practical demonstrations of many-to-many relationships in EF Core. Together, they cover convention-based configuration, join entities with additional properties, and more advanced Fluent API customization.

### Video 1 - Mastering EF Core Relationships: One-to-One, One-to-Many, and Many-to-Many in .NET — Remigiusz Zalewski

<iframe width="560" height="315" src="https://www.youtube.com/embed/BfoK6R8OIR8" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

Remigiusz Zalewski demonstrates how EF Core configures relationships using navigation properties, with a practical many-to-many example involving `Customer` and `Reservation`. The video shows how EF Core creates the join table automatically and how the resulting relationships appear in SQLite migrations and database records.

Although the tutorial also covers one-to-one and one-to-many relationships, the timestamps below focus on the many-to-many sections relevant to our basic example.

**Key timestamps:**

- [1:58 — Introducing the many-to-many relationship between customers and reservations](https://www.youtube.com/watch?v=BfoK6R8OIR8&t=118s)
- [8:56 — Defining the collection navigation properties for the relationship](https://www.youtube.com/watch?v=BfoK6R8OIR8&t=536s)
- [18:44 — Examining the generated join table and foreign keys in the migration](https://www.youtube.com/watch?v=BfoK6R8OIR8&t=1124s)
- [24:49 — Demonstrating customer-reservation associations in the database](https://www.youtube.com/watch?v=BfoK6R8OIR8&t=1489s)

This example reinforces how EF Core can manage a many-to-many relationship without an explicitly defined join entity class or additional Fluent API configuration.

### Video 2 - EF Core 6: How to build and manage relationships (Part 4) — Building SaaS with Gabriel

<iframe width="560" height="315" src="https://www.youtube.com/embed/ujf8lGv8OTo" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

Building SaaS with Gabriel demonstrates two ways to model a many-to-many relationship between `Student` and `Course`. The tutorial begins with collection navigation properties and an automatically generated join table, then introduces an explicit `CourseStudent` entity to store additional information about the association.

The demonstration also uses migrations and relationship diagrams to illustrate how the database structure changes when additional properties are introduced.

**Key timestamps:**

- [11:03 — Introducing skip navigations and join entities with payload properties](https://www.youtube.com/watch?v=ujf8lGv8OTo&t=663s)
- [12:09 — Configuring the many-to-many relationship with collection navigations](https://www.youtube.com/watch?v=ujf8lGv8OTo&t=729s)
- [14:20 — Understanding when additional association properties are useful](https://www.youtube.com/watch?v=ujf8lGv8OTo&t=860s)
- [16:13 — Configuring the explicit join entity in OnModelCreating()](https://www.youtube.com/watch?v=ujf8lGv8OTo&t=973s)

The payload example extends the concepts in our `BookCategory` example by showing when association-specific data may justify an explicit join entity. The tutorial also discusses preserving an existing join table name during a schema change; this is a migration consideration, not a general requirement for many-to-many relationships.

### Video 3 - Deep Dive into Many-to-Many: A Tour of EF Core 5.0 pt. 2 — dotnet

<iframe width="560" height="315" src="https://www.youtube.com/embed/b2klBzcALJc" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

In this Microsoft On .NET episode, Arthur Vickers from the EF Core team explains what happens internally when EF Core configures a many-to-many relationship between `User` and `Group`.

The demonstration explores automatically generated join entities, composite keys, model debugging, and explicit Fluent API configuration. It also shows how developers can customize relationship mappings without configuring every detail manually.

**Key timestamps:**

- [3:36 — Configuring a many-to-many relationship with collection navigations](https://www.youtube.com/watch?v=b2klBzcALJc&t=216s)
- [5:49 — Examining the join entity generated automatically by EF Core](https://www.youtube.com/watch?v=b2klBzcALJc&t=349s)
- [12:35 — Defining the relationship explicitly with HasMany() and WithMany()](https://www.youtube.com/watch?v=b2klBzcALJc&t=755s)
- [15:42 — Customizing the join table and foreign key column names](https://www.youtube.com/watch?v=b2klBzcALJc&t=942s)

This video provides a deeper look at the conventions behind many-to-many relationships. It uses EF Core 5 and explores shared-type join entities and more extensive configurations than our introductory `UsingEntity<BookCategory>()` example. Its main value is showing how convention-based and explicit configurations work together.

## Summary

EF Core supports many-to-many relationships through conventions or explicit join entity configuration, depending on your model's requirements.

- **Basic many-to-many:** Use collection navigation properties on both entities. EF Core configures the join table by convention.
- **Many-to-many with a join entity:** Use `UsingEntity<T>()` when you need an explicit class to represent associations or store additional information.
- **Other configurations:** Use Fluent API to customize table names, foreign keys, or navigation properties.

## Related Articles

- [One-to-Many Relationships](/modeling/relationships-one-to-many)
- [One-to-One Relationships](/modeling/relationships-one-to-one) — Learn how EF Core configures a relationship in which each entity is associated with at most one entity on the other side.
- [Cascade Delete](/saving/cascade-delete) — Understand how EF Core handles related data when entities are deleted.

## FAQ

### Do I need a `DbSet<BookCategory>` when using an explicit join entity?

No. `UsingEntity<BookCategory>()` includes the join entity in the EF Core model without requiring a `DbSet<BookCategory>` property. You can add one for convenient access to join records, but it is not required to configure the relationship.

### Can I configure a many-to-many relationship with a navigation property on only one side?

Yes. EF Core supports unidirectional many-to-many relationships, where only one entity exposes a collection navigation property. Unlike the basic example, this requires explicit relationship configuration. See the [official documentation on unidirectional many-to-many relationships](https://learn.microsoft.com/en-us/ef/core/modeling/relationships/many-to-many#unidirectional-many-to-many).

### Does changing from a convention-based relationship to an explicit join entity require a database migration?

Not necessarily. If the new configuration produces the same database schema, a migration may not be needed. However, changes to the join table, foreign key columns, or constraints may require a migration. Always review the generated migration before applying it to an existing database.