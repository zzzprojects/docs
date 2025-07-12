---
title: Conventions in Entity Framework Core
description: Conventions are a set of rules hard-baked into Entity Framework Core that govern how the model will be mapped to a database schema
canonical: /conventions
status: Published
lastmod: 2025-07-11
---

# EF Core Conventions

Conventions are a set of rules hard-baked into Entity Framework Core that govern how the model will be mapped to a database schema. Most of the time, especially with new application development, it makes sense to follow EF Core's conventions when developing the model. In the event that this is not possible, you can make use of [EF Core's configuration options](/configuration).

## Primary Key

If a property is named `ID` or `<entity name>ID` (not case-sensitive), it will be configured as the primary key. Entity Framework Core will prefer `ID` over `<entity name>ID` in the event that a class contains both. The following examples illustrate two versions of a Book entity where the `int` property follows the convention:

```csharp
public class Book
{
    public int Id { get; set; }
    public string Title { get; set; }
}
```
```csharp
public class Book
{
    public int BookId { get; set; }
    public string Title { get; set; }
}
```
EF Core will specify that the primary key column values are generated automatically by the database.

## Foreign Key

The convention for a foreign key is that it must have the same data type as the principal entity's primary key property and the name must follow one of these patterns:

- `<navigation property name><principal primary key property name>Id`   
- `<principal class name><primary key property name>Id`   
- `<principal primary key property name>Id` 

In the example model below, no foreign key property has been defined in the dependent `Book` entity. 


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
    public Author Writer { get; set; }
    public string Isbn { get; set; }
}
```
In order of precedence, the following property names are accepted:
- `WriterAuthorId`
- `AuthorAuthorId`
- `AuthorId`

As with primary keys, foreign key property name matching is not case sensitive. EF Core infers the multiplicity of a relationship from the nullability of the foreign key. If the property is not nullable, the relationship is registered as required; otherwise it is registered as optional.

### Foreign Key Shadow Properties
If you choose not to explicitly include a foreign key property in the dependent end of the relationship, EF Core will create a [shadow property](/model/shadow-properties) using the pattern `<principal primary key property name>Id`.

## Backing Fields

Backing fields are used to preserve encapsulation. EF Core will write to them and read from them where possible. EF Core will automatically map a backing field if its name matches the following patterns:

- `_<camel-cased property name>`
- `_<property name>`
- `m_<camel-cased property name>`
- `m_<property name>`

The `Publisher` property in the following example has a backing field that uses the first pattern, and the `RecommendedRetailPrice` property has a backing field that matches the third pattern:

```csharp
public class Book
{
    private Publisher _publisher;
    private decimal m_recommendedRetailPrice;

    public int BookId { get; set; }
    public string Title { get; set; }
    public int PublisherId { get; set; }
    public Publisher Publisher { 
        get { return _publisher; }
        set { _publisher = value; }
    }
    public decimal RecommendedRetailPrice {
        get { return m_recommendedRetailPrice; }
        set { m_recommendedRetailPrice = value; }
    }
}
```


## Table
Entity Framework Core will map an entity to a table with the same name as its corresponding `DbSet<TEntity>` property. EF Core will also map entities that have no corresponding `DbSet<TEntity>` property if that entity takes part in a [fully defined relationship](/conventions/one-to-many-relationship#fully-defined-relationship) with entities that have corresponding `DbSet<TEntity>` properties. EF Core will map such an entity to a table named after the class.

In the following example, EF Core will map the `Book` entity (which has a `DbSet` property in the `DbContext` class) to a table named `Books` and the `Publisher` entity (which has no `DbSet`, but takes part in a fully defined relationship with `Book`) to a table named `Publisher`:

```csharp
public class LibraryContext : DbContext
{
    public DbSet<Book> Books { get; set; }
}


public class Book
{
    public int BookId { get; set; }
    public string Title { get; set; }
    public int PublisherId { get; set; }
    public Publisher Publisher { get; set; }
}

public class Publisher
{
    public int PublisherId { get; set; }
    public string Name { get; set; }
    public ICollection<Book> Books { get; set; }
}
```


## Schema

EF Core will map objects to the `dbo` schema by default.

## Columns

EF Core will map entity properties to database columns with the same name.

## Data Types

String properties are unlimited in size and are mapped to an appropriate nullable data type determined by the database provider (e.g. `nvarchar(MAX)` in SQL Server). All other types are decided by the database provider. The easiest way to establish how .NET types are mapped to a specific database type is to create a model with properties covering all types, and then to create a [migration](/migrations) for that model. Conversely, you can create a table in the target database that includes all of the database types, and then [reverse engineer](/walkthroughs/existing-database) that to a model.

## Indexes

EF Core will always create indexes for foreign key and alternate keys.

