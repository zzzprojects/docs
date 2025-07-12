---
title: Entity Framework Core Many To Many Relationships By Convention
description: How to use the convention to establish a many-to-many relationship in Entity Framework Core
canonical: /conventions/many-to-many-relationship
status: Published
lastmod: 2025-07-11
---

# EF Core Many To Many

A many-to-many relationship can be represented using a junction table with two foreign key properties that reference the primary key properties of the related entities. Before EF Core 5.0, it was not possible to establish a many-to-many relationship in Entity Framework Core without a joining entity class and configuration via the Fluent API. 

 - In Entity Framework Core 5.0 and later, it has full support for many-to-many relationships by convention, without explicitly defining a joined entity. 
 - The convention is that, if you have two entities `A` and `B` and you want to create a many-to-many relationship between them, you need to have two navigation properties in each entity, each with a collection of the other entity. 
 - Many-to-many relationships require a collection navigation property on both sides.

Here's an example of how to create a many-to-many relationship between a `Book` and a `Category` entity by convention:

```csharp
public class Author
{
    public int AuthorId { get; set; }
    public string? FirstName { get; set; }
    public string? LastName { get; set; }
    public ICollection<Book> Books { get; set; }
}

public class Book
{
    public int BookId { get; set; }
    public string? Title { get; set; }
    public Author Author { get; set; }
    public ICollection<Category> Categories { get; set; }
}
public class Category
{
    public int CategoryId { get; set; }
    public string? CategoryName { get; set; }
    public ICollection<Book> Books { get; set; }
}
```

As you can see the `Book` class contains a collection of `Categories`, and the `Category` class contains a collection of `Books`. EF Core 5.0 and later recognizes this as a many-to-many relationship by convention and there is no need for configuration in `OnModelCreating`.

```csharp
public class LibraryContext : DbContext
{
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
        optionsBuilder.UseSqlServer(@"Data Source=(localdb)\ProjectsV13;Initial Catalog=LibraryDb;");
    }

    public DbSet<Book> Books { get; set; }
    public DbSet<Author> Authors { get; set; }
    public DbSet<Category> Categories { get; set; }
}
```

When you create a migration or call the `EnsureCreated` method, it will create the following tables including the join table.

```csharp
CREATE TABLE [dbo].[Books] (
    [BookId]   INT            IDENTITY (1, 1) NOT NULL,
    [Title]    NVARCHAR (MAX) NULL,
    [AuthorId] INT            NOT NULL,
    CONSTRAINT [PK_Books] PRIMARY KEY CLUSTERED ([BookId] ASC),
    CONSTRAINT [FK_Books_Authors_AuthorId] FOREIGN KEY ([AuthorId]) REFERENCES [dbo].[Authors] ([AuthorId]) ON DELETE CASCADE
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

EF Core will automatically create a join table to represent the many-to-many relationship between the two entities. The name of the join table will be generated based on the names of the entities, in this case, it will be `BookCategory`.

#### Further Reading

- [Many To Many Relationship Configuration](/configuration/many-to-many-relationship-configuration)
