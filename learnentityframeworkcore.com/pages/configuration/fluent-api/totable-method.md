---
title: The Fluent API ToTable Method
description: Usage of the Fluent API ToTable Method in Entity Framework Core
canonical: /configuration/fluent-api/totable-method
status: Published
lastmod: 2025-07-11
---

# EF Core ToTable

The `ToTable` method is applied to an entity to specify the name of the database table that the entity should map to. The following example specifies that the `Book` entity should map to a database table named `tbl_Book`:

```csharp
public class SampleContext : DbContext
{
    public DbSet<Book> Books { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Book>()
            .ToTable("tbl_Book");
    }
}

public class Book
{
    public int BookId { get; set; }
    public string Title { get; set; }
    public Author Author { get; set; }
}
```
An overload of the method maps the entity to a non-default schema:
```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Book>().ToTable("tbl_Book", "library");
}
```

### Data Annotations
The Data Annotation equivalent to the `ToTable` method is the [Table attribute](/configuration/data-annotation-attributes/table-attribute)
