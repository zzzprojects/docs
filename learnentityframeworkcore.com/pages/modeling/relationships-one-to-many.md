---
title: Entity Framework Core One-To-Many Relationships Conventions
description: How to design your model so that it maps to a one-to-many relationship by convention in Entity Framework Core
canonical: /modeling/relationships-one-to-many
status: Published
lastmod: 2026-11-18
---

# EF Core One-to-Many Relationships

A one-to-many relationship allows one entity to be associated with multiple related entities. For example, an author can have many books, while each book belongs to at most one author. EF Core can discover these relationships by convention; for explicit Fluent API mapping, see [One-to-Many Relationship Configuration](/configuration/one-to-many-relationship-configuration).

## Basic One-to-Many Relationship

```csharp
public class Author
{
    public int AuthorId { get; set; }
    public string FirstName { get; set; }
    public string LastName { get; set; }
    public ICollection<Book> Books { get; set; }
}

public class Book
{
    public int BookId { get; set; }
    public string Title { get; set; }
}
```

EF Core discovers this relationship by convention through the `Books` collection in the `Author` entity. This means one author can be associated with multiple books without explicitly configuring the relationship.

Because `Book` does not define a foreign key property, EF Core creates a [shadow foreign key](/model/shadow-properties) named `AuthorId`. This foreign key identifies the author associated with each book. It exists in the EF Core model but is not declared in the C# class. EF Core manages its value internally, and the database still contains the corresponding foreign key column.

In this example, the foreign key is nullable by convention, making the relationship optional. A book can therefore exist without an associated author.

The relationship can also be configured as required using `IsRequired()`.

For more configuration options, see the [official EF Core one-to-many documentation](https://learn.microsoft.com/en-us/ef/core/modeling/relationships/one-to-many).

The following excerpt from the previously published SQL Server migration illustrates how the shadow foreign key is represented in the `Books` table:

```csharp
migrationBuilder.CreateTable(
    name: "Books",
    columns: table => new
    {
        BookId = table.Column<int>(nullable: false)
            .Annotation("SqlServer:ValueGenerationStrategy", SqlServerValueGenerationStrategy.IdentityColumn),
        AuthorId = table.Column<int>(nullable: true),
        Title = table.Column<string>(nullable: true)
    },
    constraints: table =>
    {
        table.PrimaryKey("PK_Books", x => x.BookId);
        table.ForeignKey(
            name: "FK_Books_Authors_AuthorId",
            column: x => x.AuthorId,
            principalTable: "Authors",
            principalColumn: "AuthorId",
            onDelete: ReferentialAction.Restrict);
    });
```

The historical migration specifies `ReferentialAction.Restrict` for deletes. The `ReferentialAction` enumeration also includes `NoAction`, `SetNull`, `Cascade`, and `SetDefault`. These values describe referential actions for migrations; their database behavior can vary by provider. See [Referential Constraint Action Options](/relationships/referential-constraint-action-options) for an explanation of the options.

The corresponding SQL Server table definition from the original documentation is shown below. Notice that the `AuthorId` column is nullable, even though it is not declared in the `Book` class.

```sql
CREATE TABLE [Books] (
    [BookId] int NOT NULL IDENTITY,
    [AuthorId] int,
    [Title] nvarchar(max),
    CONSTRAINT [PK_Books] PRIMARY KEY ([BookId]),
    CONSTRAINT [FK_Books_Authors_AuthorId] FOREIGN KEY ([AuthorId])
        REFERENCES [Authors] ([AuthorId]) ON DELETE NO ACTION
);
```

These historical snippets are preserved from the original article and were not regenerated with EF Core 10. The exact migration syntax and generated SQL may differ depending on the EF Core version and database provider.

## Inverse Navigation Property

Another way to define a one-to-many relationship by convention is to include an inverse navigation property in the dependent entity.

```csharp
public class Author
{
    public int AuthorId { get; set; }
    public string FirstName { get; set; }
    public string LastName { get; set; }
    public ICollection<Book> Books { get; set; }
}

public class Book
{
    public int BookId { get; set; }
    public string Title { get; set; }
    public Author Author { get; set; }
}
```

Adding the `Author` property to `Book` lets you access the author of a book, while the `Books` collection lets you access all books belonging to an author. This allows navigation in both directions without declaring an explicit foreign key property.

EF Core still creates a shadow foreign key because `Book` does not declare an `AuthorId` property. With nullable reference types disabled, the relationship is optional by convention. With nullable reference types enabled, the nullability of the `Author` navigation affects whether the relationship is required or optional.

## Optional One-to-Many Relationship

The previous examples use shadow foreign keys. You can also declare a nullable foreign key explicitly to define an optional relationship.

For example, a tenant can exist without being assigned to a property:

```csharp
public class Property
{
    public int PropertyId { get; set; }
    public string Address { get; set; }
    public List<Tenant> Tenants { get; set; }
}

public class Tenant
{
    public int TenantId { get; set; }
    public string Name { get; set; }
    public string Email { get; set; }
    public int? PropertyId { get; set; }
    public Property Property { get; set; }
}
```

EF Core discovers this relationship by convention through the `Property.Tenants` and `Tenant.Property` navigation properties, using `PropertyId` as the foreign key.

The nullable `PropertyId` foreign key makes this relationship optional. When nullable reference types are enabled, declaring the `Tenant.Property` navigation as `Property?` also reflects that the related property may be absent.

By default, EF Core uses `ClientSetNull` for optional relationships. When a property is deleted, EF Core sets `PropertyId` to `null` for related tenants that are loaded and tracked. If the tenants are not loaded, the database normally prevents the deletion unless a database-side action such as `ON DELETE SET NULL` has been configured. For details, see [Cascade Delete in EF Core](/saving/cascade-delete).

## One-to-Many with Explicit Foreign Key

A one-to-many relationship can also be configured using an explicit, non-nullable foreign key in the dependent entity.

In the following example, `Book` defines both the `AuthorId` foreign key and the `Author` navigation property:

```csharp
public class Author
{
    public int AuthorId { get; set; }
    public string FirstName { get; set; }
    public string LastName { get; set; }
    public ICollection<Book> Books { get; set; }
}

public class Book
{
    public int BookId { get; set; }
    public string Title { get; set; }
    public int AuthorId { get; set; }
    public Author Author { get; set; }
}
```

EF Core discovers this relationship by convention. The `AuthorId` property is the foreign key, and because it is a non-nullable `int`, the relationship is required.

This means that each book must be associated with an author, although an author can exist without any books.

By convention, EF Core configures cascade delete for required relationships. Deleting an author therefore deletes their related books unless a different delete behavior is configured.

### Without an Inverse Navigation Property

You can also define the same required relationship without the `Book.Author` navigation property:

```csharp
public class Author
{
    public int AuthorId { get; set; }
    public string FirstName { get; set; }
    public string LastName { get; set; }
    public ICollection<Book> Books { get; set; }
}

public class Book
{
    public int BookId { get; set; }
    public string Title { get; set; }
    public int AuthorId { get; set; }
}
```

EF Core still discovers the required relationship through the `Author.Books` collection and the non-nullable `Book.AuthorId` foreign key. An inverse navigation property is not required to configure the relationship.

For an additional example of this configuration, see the [official EF Core documentation on one-to-many relationships without navigation to the principal](https://learn.microsoft.com/en-us/ef/core/modeling/relationships/one-to-many#one-to-many-without-navigation-to-principal).

## Other One-to-Many Configurations

EF Core supports additional one-to-many configurations for relationships with different navigation properties or key requirements.

| Configuration | When to use it | Documentation |
|---|---|---|
| Without navigation to dependents | The principal entity does not have a collection navigation property. | [Microsoft Learn](https://learn.microsoft.com/en-us/ef/core/modeling/relationships/one-to-many#one-to-many-without-navigation-to-dependents) |
| Without navigations | Neither entity defines navigation properties, so the relationship must be configured explicitly. | [Microsoft Learn](https://learn.microsoft.com/en-us/ef/core/modeling/relationships/one-to-many#one-to-many-with-no-navigations) |
| Alternate key | The foreign key references an alternate key instead of the principal's primary key. | [Microsoft Learn](https://learn.microsoft.com/en-us/ef/core/modeling/relationships/one-to-many#one-to-many-with-alternate-key) |
| Composite foreign key | The relationship uses multiple foreign key properties to reference a composite key. | [Microsoft Learn](https://learn.microsoft.com/en-us/ef/core/modeling/relationships/one-to-many#one-to-many-with-composite-foreign-key) |

When conventions do not produce the intended relationship, Fluent API methods such as `HasMany()`, `WithOne()`, and `HasForeignKey()` allow you to configure it explicitly.

## External Resources

The following videos demonstrate different ways to define and configure one-to-many relationships in EF Core. Together, they cover explicit foreign keys, shadow foreign keys, required and optional relationships, and navigation properties.

### Master One-to-Many EF Core Relationships in 10 Minutes | ASP.NET Core Minimal API Tutorial — Poorna Soysa

<iframe width="560" height="315" src="https://www.youtube.com/embed/1P_O7j6J-m0" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

Poorna Soysa demonstrates a one-to-many relationship between `Customer` and `Order`, using an explicit foreign key, navigation properties, and Fluent API. The example also shows how the relationship is represented in SQL Server after applying a migration.

- [0:56 — Introducing the one-to-many relationship between Customer and Order](https://www.youtube.com/watch?v=1P_O7j6J-m0&t=56s)
- [1:53 — Defining the CustomerId foreign key and navigation properties](https://www.youtube.com/watch?v=1P_O7j6J-m0&t=113s)
- [3:11 — Configuring the relationship with Fluent API](https://www.youtube.com/watch?v=1P_O7j6J-m0&t=191s)
- [4:36 — Generating a migration and inspecting the database](https://www.youtube.com/watch?v=1P_O7j6J-m0&t=276s)

Although EF Core can discover this relationship by convention, Fluent API provides additional control when needed.

### Shadow Properties - EF Core - Foreign Keys — Full Stack Amigo

<iframe width="560" height="315" src="https://www.youtube.com/embed/SSqB_Z48co4" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

Full Stack Amigo demonstrates how EF Core creates and manages a shadow foreign key in a one-to-many relationship between `User` and `Tweet`. The example shows that a foreign key can exist in the EF Core model and database without being declared as a property in the dependent C# class.

- [0:00 — Removing the explicit foreign key and dependent navigation](https://www.youtube.com/watch?v=SSqB_Z48co4&t=0s)
- [1:30 — Inspecting the UserId shadow foreign key in SQL Server](https://www.youtube.com/watch?v=SSqB_Z48co4&t=90s)
- [2:30 — Accessing and querying shadow property values](https://www.youtube.com/watch?v=SSqB_Z48co4&t=150s)
- [4:48 — Configuring a required shadow foreign key with IsRequired()](https://www.youtube.com/watch?v=SSqB_Z48co4&t=288s)

Shadow foreign keys can be optional or required. Their nullability depends on the relationship conventions and configuration, not simply on whether the foreign key is declared in the C# class.

### EF Core 6: How to build and manage relationships (Part 4) — Building SaaS with Gabriel

<iframe width="560" height="315" src="https://www.youtube.com/embed/ujf8lGv8OTo" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

Building SaaS with Gabriel demonstrates a one-to-many relationship between `Grade` and `Student`. The relevant sections show how EF Core initially generates a nullable shadow foreign key and how declaring a non-nullable `GradeId` property changes the relationship to required. The example also introduces an inverse navigation property.

- [4:31 — Introducing the Grade–Student one-to-many relationship](https://www.youtube.com/watch?v=ujf8lGv8OTo&t=271s)
- [6:28 — Examining the migration with a nullable shadow foreign key](https://www.youtube.com/watch?v=ujf8lGv8OTo&t=388s)
- [7:31 — Declaring a non-nullable GradeId to require a principal](https://www.youtube.com/watch?v=ujf8lGv8OTo&t=451s)
- [8:47 — Adding the Student.Grade inverse navigation property](https://www.youtube.com/watch?v=ujf8lGv8OTo&t=527s)

The most useful comparison is between the migrations before and after adding `int GradeId`. The one-to-many demonstration runs from approximately 4:31 to 11:03; later sections cover other relationship types.

## Summary

EF Core supports one-to-many relationships through conventions or explicit configuration. The appropriate configuration depends on whether the relationship is required or optional and which foreign key and navigation properties your entities expose.

- **Shadow foreign keys:** EF Core can create a foreign key when no corresponding property is declared in the dependent entity.
- **Optional and required relationships:** A nullable foreign key makes the relationship optional, while a non-nullable foreign key makes it required.
- **Navigation properties:** Relationships can have navigations in both directions, one direction, or neither.
- **Explicit configuration:** Fluent API provides control when conventions do not match the intended relationship.

## Related Articles

- [Relationships](/relationships) — Review how primary keys, foreign keys, and navigation properties connect entities.
- [One-to-One Relationships](/modeling/relationships-one-to-one) — Configure relationships with one dependent per principal.
- [Many-to-Many Relationships](/modeling/relationships-many-to-many) — Configure relationships where entities can be associated with multiple entities on both sides.
- [Managing One-to-Many Relationships](/relationships/managing-one-to-many-relationships) — Create, update, and remove relationships between existing entities.

## FAQ

### Can a shadow foreign key be required?

Yes. A shadow foreign key can be required even when no foreign key property is declared in the dependent entity. Use `IsRequired()` in Fluent API to configure a required relationship when EF Core would otherwise treat it as optional.

### Does a required one-to-many relationship mean the principal must have dependents?

No. A required relationship means each dependent must be associated with a principal. It does not require the principal to have any dependents. For example, an author can exist without books, even when every book must have an author.

### Are navigation properties required for a one-to-many relationship?

No. A one-to-many relationship can have navigations on both sides, on only one side, or on neither side. EF Core can discover many relationships by convention when suitable navigations are present. Without navigation properties, the relationship must be configured explicitly.

### Does a non-nullable foreign key automatically load the related entity?

No. A non-nullable foreign key makes the relationship required, but it does not automatically load the related entity into its navigation property. You can use `Include()` to load related data as part of a query. See [Eager Loading in EF Core](/querying/eager-loading) for more details.
