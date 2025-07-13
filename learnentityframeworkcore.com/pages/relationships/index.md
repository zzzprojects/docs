---
title: Introduction to Relationships
description: An introduction to relationships in relational databases and how they are represented in Entity Framework Core
canonical: /relationships
status: Published
lastmod: 2025-07-13
---

# EF Core Relationships

Relational databases are data stores whose structure is based on how items of data are related to each other. 

A key benefit to taking a relational view of data is to reduce duplication. For example, you might want to record data about people in a town. If you took a non-relational approach to record this data, you would store the person's name together with their address, place of work, school, and so on as individual data items. Where multiple people live at the same address or go to the same school, you would record the address or school details in multiple places. If the school name ever changed, you would have to update it in every data item in which it appeared, which is a time-consuming and error-prone task. 

In a relational database, each _entity_ such as the person, the school, and the place of work is stored in separate tables and unique instances of the entity are identified by a Primary Key value. Relationships or associations between entities are defined in a database by the existence of Foreign Keys. 

## One-To-Many
The following diagram depicts a relationship between the Books and Authors tables in a database:

![Database Relationship](/images/17-08-2016-14-01-13.png)

Each table has a Primary Key (PK) that uniquely defines each instance of an entity (or row) within the table. The Books table's PK is `BookId`, and the Authors table's PK is `AuthorId`. The `AuthorId` column in the Books table is a Foreign Key (FK), linking a book to its author.  The book is the _dependent_ entity in the relationship. Its integrity depends on a valid reference to an author. The author becomes the _principal_ entity. Using foreign keys, you can link one author row in the database to many book rows. This type of relationship is the most commonly found and is known as a **One-To-Many** relationship.

Depending on the diagramming software that you use, the side of the relationship that has a _multiplicity_ of 1 is normally depicted by a figure **1** or a key. The side of the relationship with a _multiplicity_ of many is usually depicted by an asterisk (*) or an infinity symbol (&#8734;). _Multiplicity_ describes the potential number of items that can be found at one end of a relationship.

## Navigation Properties
Relationships between entities in an Entity Framework model are defined by _Navigation Properties_. A navigation property is one that the database provider being used cannot map to a primitive (or scalar) type. The following code depicts the model representation of the database example above:

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

Both classes contain properties whose types can be mapped to existing database types - `int`, and `string`, but they also contain properties that cannot be mapped. There is no equivalent type in databases for the `Book` type or the `Author` type. Therefore they are viewed as navigation properties. 

The definition of the `Book` class allows each book to have at most one author through the `Author` navigation property (a _Reference_ navigation property having a multiplicity of zero or one), and the definition of the `Author` class allows each author to have many books through the `Books` navigation property (a _Collection_ navigation property having a multiplicity of _many_). Together, they define a one-to-many relationship. The principal entity in a one-to-many relationship is the one that has the collection navigation property, and the dependent entity is the one with the reference navigation property.

Navigation properties enable navigation of the association between the two types through code:

```csharp
book.Author = new Author();

foreach (var book in author.Books)
{
    ...
```
#### Further reading
- [Managing One-To-Many Relationships](/relationships/managing-one-to-many-relationships)
- [Entity Framework One-To-Many Relationship Conventions](/conventions/one-to-many-relationship)
- [Entity Framework One-To-Many Relationship Configurations](/configuration/one-to-many-relationship-configuration)


## Many To Many

The second most common type of relationship is known as a **Many-To-Many** relationship. The following diagram shows how this appears in a database diagram.

Each book can belong to many categories, and each category can contain many books. This type of relationship is managed in a database through the use of a join table (also known among other things as a bridging, junction, or linking table). This type of relationship is defined in code by the inclusion of collection properties in each of the entity classes: 


```csharp
public class Book
{
    public int BookId { get; set; }
    public string Title { get; set; }
    public Author Author { get; set; }
    public ICollection<Category> Categories { get; set; }
} 

public class Category
{
    public int CategoryId { get; set; }
    public string CategoryName { get; set; }
    public ICollection<Book> Books { get; set; }
}
```
Note: While in previous versions of EF, this approach was sufficient to have EF generate the appropriate tables and form the correct relationships, in EF Core 1.0, it isn't. It is necessary to include an entity within the model to represent the join table.

#### Further reading

- [Entity Framework Core Many To Many Relationship Configuration](/conventions/many-to-many-relationship)

## One To One
A one-to-one (or more usually a one-to-zero or one) relationship exists when only one row of data in the principal table is linked to zero or one row in a dependent table. The following diagram illustrates this relationship between an Authors table and the AuthorBiographies table:

    
![One to one relationship](/images/27-09-2016-08-56-37.png)

 The following example shows how this relationship is modeled in code:

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
In this example of a one-to-one relationship, each Author can have one, and only one biography.

One reason for implementing this kind of relationship is when you are working with inheritance. For example, you may have a Vehicle entity, with subclasses such as Car, Truck, Motorcycle, etc. Other reasons include database design and/or efficiency. For example, you may want to apply extra database security to the dependent table because it contains confidential information (an employee's health record, for example), or you just want to move data that isn't referenced very often into a separate table to improve search and retrieval times for data that is used all the time.

## Table Splitting

EF Core (from version 2.0) also supports _table splitting_, a technique that enables you to use a single table to represent both entities in a one-to-one relationship where separation into multiple tables is not required. Using this feature, the one-to-one relationship illustrated above will be stored in a single database table together:

![Table splitting](/images/20-02-2018-08-39-09.png)

Each entity needs to be defined separately, and it must share the same primary key value. This type of relationship requires [configuration](/configuration/one-to-one-relationship-configuration#configuring-a-relationship-that-uses-table-splitting)

#### Further Reading
- [Entity Framework One To One Relationship Conventions](/conventions/one-to-one-relationship)
- [Entity Framework One To One Relationship Configurations](/configuration/one-to-one-relationship-configuration)









