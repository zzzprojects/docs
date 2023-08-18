---
title: The Fluent API IsRowVersion Method
description: Usage of the Fluent API IsRowVersion Method in Entity Framework Core
canonical: /configuration/fluent-api/isrowversion-method
status: Published
lastmod: 2023-02-27
---

# EF Core IsRowVersion

The `IsRowVersion` method is used to denote that a property should take part in [concurrency management](/concurrency). 

When applied to a byte array property, the `IsRowVersion` method denotes that the property should map to a database type that provides automatic row-versioning, such as the SQL Server `rowversion` type:
```csharp
public class SampleContext : DbContext
{
    public DbSet<Author> Authors { get; set; }
    
    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Author>()
            .Property(a => a.RowVersion)
            .IsRowVersion();
    } 
}

public class Author
{
    public int AuthorId { get; set; }
    public string FirstName { get; set; }
    public string LastName { get; set; }
    public string Biography { get; set; }
    public ICollection<Book> Books { get; set; }
    public byte[] RowVersion { get; set; }
}
```

The `IsRowVersion` method was introduced in EF Core 1.1 as a wrapper around the [IsConcurrencyToken method chained with the ValueGeneratedOnAddOrUpdate method](/configuration/fluent-api/isconcurrencytoken-method).

### Data Annotations
The Data Annotations equivalent to the `IsRowVersion` method is the [Timestamp attribute](/configuration/data-annotation-attributes/timestamp-attribute).

#### Further Reading
- [Concurrency Management](/concurrency)
