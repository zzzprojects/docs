---
title: The Fluent API HasColumnName Method
description: Usage of the Fluent API HasColumnName Method in Entity Framework Core
canonical: /configuration/fluent-api/hascolumnname-method
status: Published
lastmod: 2023-02-26
---

# EF Core HasColumnName

The `HasColumnName` attribute is applied to a property to specify the database column that the property should map to when the entity's property name and the database column name differ. The following example maps the `Title` property in the `Book` entity to a database column named `Description` in the Books table:

```csharp
public class SampleContext : DbContext
{
    public DbSet<Book> Books { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Book>()
            .Property(b => b.Title).HasColumnName("Description");
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

The data annotations equivalent to the `HasColumnName` method is the [Column attribute](/configuration/data-annotation-attributes/column-attribute).
