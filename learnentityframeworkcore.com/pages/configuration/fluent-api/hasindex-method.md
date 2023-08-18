---
title: The Fluent API HasIndex Method
description: Usage of the Fluent API HasIndex Method in Entity Framework Core
canonical: /configuration/fluent-api/hasindex-method
status: Published
lastmod: 2023-02-27
---

# EF Core HasIndex
The Entity Framework Core Fluent API `HasIndex` method is used to create a database index on the column mapped to the specified entity property. By default, indexes are created for foreign keys and alternate keys. You may wish to create indexes on other properties to speed up data retrieval:

```csharp
public class SampleContext : DbContext
{
    public DbSet<Book> Books { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Book>()
            .HasIndex(b => b.Isbn);
    }
}


public class Book
{
    public int BookId { get; set; }
    public string Title { get; set; }
    public string Isbn { get; set; }
}
```
This will result in a non-unique index being created. 

The `HasIndex` method returns an instance of the `IndexBuilder` object via which you can further configure the index that gets created. You can specify that the index can only contain unique values by chaining a call to the `IsUnique` method:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Book>()
        .HasIndex(b => b.Isbn)
        .IsUnique();
}
```

While similar in most practical aspects, this is not the same as creating a unique constraint on the column, which can be achieved by [creating an alternate key](/configuration/fluent-api/hasalternatekey-method) on the property.

You can also provide your own name for the index by chaining a call to the `HasName` method:
```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Book>()
        .HasIndex(b => b.Isbn)
        .IsUnique();
        .HasName("MyIndexName");
}
```
### Clustered Index
Depending on the database provider that you are using, you may be able to specify that an index should be clustered. For example, if you are using MS SQL Server, the provider introduces an extension method, `ForSqlServerIsClustered`
```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Book>()
        .HasIndex(b => b.Isbn)
        .IsUnique();
        .ForSqlServerIsClustered();
}
```
### Multi-Column Index

If you wish to apply an index on more than one column, you specify the properties that form the index by passing them in as properties of an anonymous type to the `HasIndex` method.

```csharp
public class SampleContext : DbContext
{
    public DbSet<Patient> Patients { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Patient>()
            .HasIndex(p => new { p.Ssn, p.DateOfBirth});
    }
}


public class Patient
{
    public int PatientId { get; set; }
    public string Ssn { get; set; }
    public DateTime DateOfBirth { get; set; }
}
```
### Data Annotations
The equivalent Data Annotation Attribute to the `HasIndex` method is the [Index attribute](/configuration/data-annotation-attributes/index-attribute)
