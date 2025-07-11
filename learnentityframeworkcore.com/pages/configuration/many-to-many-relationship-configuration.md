---
title: Configuring Many To Many Relationship in Entity Framework Core
description: How to configure a many-to-many relationship in Entity Framework Core
canonical: /configuration/many-to-many-relationship-configuration
status: Published
lastmod: 2025-07-11
---

# EF Core Many To Many Relationship

A many-to-many relationship occurs between entities when a one-to-many relationship between them works both ways. A book can appear in many categories and a category can contain many books. This type of relationship is represented in a database by a _join_ table (also known among other things as a _bridging_, _junction_ or _linking_ table). 

Typically, the join table contains just the entity key values of each side of the relationship. In the books and categories example, this would result in a field for the `BookId` and one for the `CategoryId`. 

A many-to-many relationship is defined in code  by the inclusion of collection properties in each of the entities - The `Categories` property in the `Book` class, and the `Books` property in the `Category` class: 

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
In previous versions of Entity Framework, this model definition was sufficient for EF to imply the correct type of relationship and to generate the join table for it. In EF Core up to and including 3.x,  it is necessary to include an entity in the model to represent the join table, and then add navigation properties to either side of the many-to-many relationships that point to the joining entity instead:

```csharp
public class Book
{
    public int BookId { get; set; }
    public string Title { get; set; }
    public Author Author { get; set; }
    public ICollection<BookCategory> BookCategories { get; set; }
}  

public class Category
{
    public int CategoryId { get; set; }
    public string CategoryName { get; set; }
    public ICollection<BookCategory> BookCategories { get; set; }
}  

public class BookCategory
{
    public int BookId { get; set; }
    public Book Book { get; set; }
    public int CategoryId { get; set; }
    public Category Category { get; set; }
}
```
The join table will be named after the joining entity (`BookCategory` in this case) by convention. The relationship also needs to be configured via the Fluent API for EF Core to be able to map it successfully:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<BookCategory>()
        .HasKey(bc => new { bc.BookId, bc.CategoryId });  

    modelBuilder.Entity<BookCategory>()
        .HasOne(bc => bc.Book)
        .WithMany(b => b.BookCategories)
        .HasForeignKey(bc => bc.BookId);  

    modelBuilder.Entity<BookCategory>()
        .HasOne(bc => bc.Category)
        .WithMany(c => c.BookCategories)
        .HasForeignKey(bc => bc.CategoryId);
}
```
The primary key for the join table is a [composite key](/configuration/fluent-api/haskey-method#composite-keys) comprising both of the foreign key values. In addition, both sides of the many-to-many relationship are configured using the [HasOne](/configuration/fluent-api/hasone-method), [WithMany](/configuration/fluent-api/withmany-method), and [HasForeignKey](/configuration/fluent-api/hasforeignkey-method) Fluent API methods.

This is sufficient if you want to access book category data via the `Book` or `Category` entities. If you want to query `BookCategory` data directly, you should also add a `DbSet` for the join table to the context:

```csharp
public class SampleContext : DbContext
{
    public DbSet<Book> Books { get; set; }
    public DbSet<Category> Categories { get; set; }
    public DbSet<BookCategory> BookCategories { get; set; }
}
```

### Note

The EF Team is removing the need for a joined entity from .NET Core 5. The issue can be [tracked at GitHub](https://github.com/aspnet/EntityFramework/issues/1368) and also [here](https://github.com/aspnet/EntityFrameworkCore/issues/10508). 



