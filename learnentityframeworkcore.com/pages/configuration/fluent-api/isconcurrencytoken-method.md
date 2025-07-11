---
title: The Fluent API IsConcurrencyToken Method
description: Usage of the Fluent API IsConcurrencyToken Method in Entity Framework Core
canonical: /configuration/fluent-api/isconcurrencytoken-method
status: Published
lastmod: 2025-07-11
---

# EF Core IsConcurrencyToken

The `IsConcurrencyToken` method is used to specify that a property should be included in a `WHERE` clause in an `UPDATE` or `DELETE` statement as part of [concurrency management](/concurrency).

```csharp
public class SampleContext : DbContext
{
    public DbSet<Author> Authors { get; set; }
    
    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Author>()
            .Property(a => a.FirstName).IsConcurrencyToken();
         modelBuilder.Entity<Author>()
            .Property(a => a.LastName).IsConcurrencyToken();
    } 
}

public class Author
{
    public int AuthorId { get; set; }
    public string FirstName { get; set; }
    public string LastName { get; set; }
    public string Biography { get; set; }
    public ICollection<Book> Books { get; set; }
}
```

When applied to a byte array property in combination with the [ValueGeneratedOnAddOrUpdate method](/configuration/fluent-api/valuegeneratedonaddorupdate-method), the `IsConcurrencyToken` method denotes that the property should map to a database type that provides automatic row-versioning, such as the SQL Server `rowversion` type:
```csharp
public class SampleContext : DbContext
{
    public DbSet<Author> Authors { get; set; }
    
    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Author>()
            .Property(a => a.RowVersion)
            .IsConcurrencyToken()
            .ValueGeneratedOnAddOrUpdate();
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



### Data Annotations
The Data Annotations equivalents to the `IsConcurrencyToken` method are the [ConcurrencyCheck attribute](/configuration/data-annotation-attributes/concurrencycheck-attribute) and the [Timestamp attribute](/configuration/data-annotation-attributes/timestamp-attribute).

#### Further Reading
- [Concurrency Management](/concurrency)
