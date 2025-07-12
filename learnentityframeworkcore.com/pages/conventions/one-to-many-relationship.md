---
title: Entity Framework Core One-To-Many Relationships Conventions
description: How to design your model so that it maps to a one-to-many relationship by convention in Entity Framework Core
canonical: /conventions/one-to-many-relationship
status: Published
lastmod: 2025-07-11
---

# EF Core One to Many

The easiest way to configure a one-to-many relationship is by convention. EF Core will create a relationship if an entity contains a [navigation property](/relationships#navigation-properties). Therefore, the minimum required for a relationship is the presence of a navigation property in the principal entity:

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
The navigation property in this example is the `Books` property in the `Author` entity. It cannot be mapped to an equivalent data type within the database. EF Core will generate a [shadow property](/model/shadow-properties) for the foreign key named `AuthorId`, which will map to a nullable `AuthorId` foreign key column in the Books table in the database. Here is a snippet of the `Up` method in a corresponding Migration class:

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


The action that should take place when updating or deleting the principal in a one-to-many relationship is specified via the `ReferentialAction` enumeration, which has 5 possible values: 
- `NoAction` 
- `Restrict` 
- `SetNull`
- `Cascade`
- `SetDefault`

Each of these results in a [different referential constraint action option](/relationships/referential-constraint-action-options) being specified by EF Core's migration framework. When you use a single navigation property to define a one-to-many relationship, `ON UPDATE` is left at its default value of `NO ACTION` and the `Restrict` option is used for deletes, which is translated to `ON DELETE NO ACTION` for SQL Server as demonstrated in the resulting SQL for creating the Books table:
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

## Inverse Navigation Property

The second option available for defining one-to-many relationships by convention is to include an _inverse_ navigation property in the dependent entity - the `Author` property in the `Book` entity below - in addition to the navigation property in the principal entity, which results in both ends of the relationship being specified:

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
This approach produces the same results in respect of the shadow property and referential constraints being generated as in the previous approach.


## Fully Defined Relationship

In the third approach, the relationship is considered to be _fully defined_ when both ends of the navigation are present together with a _foreign key property_:
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

In this case, the referential constraint will be specified as `Cascade` on delete, based on the relationship being a required relationship i.e. the foreign key is not nullable.  

The same configuration can be achieved by omitting the inverse navigation property, specifying only the foreign key property and the principal navigational property:
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
### Optional Relationships

In the previous example, the relationship is a _required_ relationship. That means that a dependent cannot exist without its principal. If an author is deleted, all of the authors' books must also be deleted. EF Core will set up this behavior automatically if you explicitly include a non-nullable foreign key property, otherwise as you have seen, EF Core prevents the deletion of authors that are currently associated with books.

Logically, a book cannot exist without an author, so that relationship is always going to be a required one. The same cannot be said for the relationship between, say, a tenant and a property to let. A tenant can exist without being assigned to a property. This is an example of an _optional relationship_. This is the type of relationship that EF Core generates automatically if you do not include a foreign key property in the dependent entity, as you have seen in the first and second examples above. You can also explicitly create an optional relationship by including a nullable foreign key property in the dependent entity:

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

The `Tenant` entity includes a nullable foreign key property, `PropertyId`. The `?` operator is shorthand for `Nullable<int>`.  When a tenant first registers with a lettings agency, they won't have a property assigned to them. Therefore the `PropertyId` column in the database will be `NULL`, signifying a homeless state. If a property comes off the market and is no longer available, the tenants in that property will still exist, albeit in a homeless state. 

By default, this kind of relationship will be created with a `ReferentialAction` of `Restrict`, which will prevent the deletion of properties assigned to tenants in the database. You must use the Fluent API to enable the `SetNull` behavior on this relationship, which will result in the `PropertyId` value being set to `NULL` on tenants linked to properties being deleted. 

#### Further Reading

- [Relationships](/relationships)
- [Shadow Properties](/model/shadow-properties)
- [Fluent API Relationship Configuration](/configuration/one-to-many-relationship-configuration)




