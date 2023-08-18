---
title: The Fluent API HasForeignKey Method
description: Usage of the Fluent API HasForeignKey Method in Entity Framework Core
canonical: /configuration/fluent-api/hasforeignkey-method
status: Published
lastmod: 2023-02-27
---

# EF Core HasForeignKey

The Entity Framework Core Fluent API `HasForeignKey` method is used to specify which property is the foreign key in a relationship. 

In the following example, the `AuthorFK` property in the `Book` entity does not follow [Entity Framework Core's convention for foreign key names](/conventions#foreign-key). Left as it is, Entity Framework Core will create an `AuthorFK` field *and* an `AuthorId` field which it will configure as a foreign key:

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
    public int AuthorFK { get; set; }
    public Author Author { get; set; }
}
```
The `AuthorFK` property is configured as the foreign key in the `OnModelCreating` method:
```csharp
public class SampleContext : DbContext
{
    public DbSet<Author> Authors { get; set; }
    public DbSet<Book> Books { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Book>()
            .HasForeignKey(p => p.AuthorFK);
    }

```

### Data Annotations
The data annotations equivalent to the `HasForeignKey` method is the [ForeignKey attribute](/configuration/data-annotation-attributes/foreignkey-attribute).
