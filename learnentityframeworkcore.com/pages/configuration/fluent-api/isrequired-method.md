---
title: The Fluent API  IsRequired Method
description: Usage of the Fluent API IsRequired Method in Entity Framework Core
canonical: /configuration/fluent-api/isrequired-method
status: Published
lastmod: 2023-02-27
---

# EF Core IsRequired

The Entity Framework Core Fluent API includes two `IsRequired` methods. One is available on the `PropertyBuilder` type and is applied to a property to configure the mapped database column as not nullable.  In the example below, the `Title` property of the `Book` entity will be configured as not null. By default, strings are mapped to nullable columns in the database:

```csharp
public class SampleContext : DbContext
{
    public DbSet<Book> Books { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Book>()
            .Property(b => b.Title)
            .IsRequired();
    }
}

public class Book
{
    public int BookId { get; set; }
    public string Title { get; set; }
    public Author Author { get; set; }
}
```

The second `IsRequired` method is available on the `ReferenceCollectionBuilder` type and is applied to relationships to specify that they should not be optional. This option might be used to override the fact that by default, [shadow property](/model/shadow-properties) foreign keys are nullable:
```csharp
public class SampleContext : DbContext
{
    public DbSet<Book> Books { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Author>()
            .HasMany(a => a.Books)
            .WithOne(b => b.Author)
            .IsRequired();
    }
}

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




### Data Annotations
The Data Annotations equivalent for the `IsRequired` method is the [Required attribute](/configuration/data-annotation-attributes/required-attribute).

#### Further Reading
- [Configuring One To Many Relationships](/configuration/one-to-many-relationship-configuration#required-relationship)
