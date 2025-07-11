---
title: The Fluent API HasMaxLength Method
description: Usage of the Fluent API HasMaxLength Method in Entity Framework Core
canonical: /configuration/fluent-api/hasmaxlength-method
status: Published
lastmod: 2025-07-11
---

# EF Core HasMaxLength

The `HasMaxLength` method is applied to a property to specify a maximum number of characters or bytes for the column that the property should map to. The following example specifies that the `Title` column in the Books table is to have a maximum length of 150 characters instead of the default which is unlimited:

```csharp
public class SampleContext : DbContext
{
    public DbSet<Book> Books { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Book>()
            .Property(b => b.Title).HasMaxLength(150);
    }
}

public class Book
{
    public int BookId { get; set; }
    public string Title { get; set; }
    public Author Author { get; set; }
}
```

### Data Annotations
The Data Annotations equivalents to the `HasMaxLength` method are the [MaxLength attribute](/configuration/data-annotation-attributes/maxlength-attribute) and the [StringLength attribute](/configuration/data-annotation-attributes/stringlength-attribute).
