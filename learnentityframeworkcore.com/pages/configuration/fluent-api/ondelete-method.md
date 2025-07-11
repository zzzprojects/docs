---
title: The Fluent API OnDelete Method
description: Usage of the Fluent API OnDelete Method in Entity Framework Core
canonical: /configuration/fluent-api/ondelete-method
status: Published
lastmod: 2025-07-11
---

# EF Core OnDelete

The Entity Framework Core Fluent API `OnDelete` method is used to specify the action which should take place on a dependent entity in a relationship when the principal is deleted.

The `OnDelete` method takes a `DeleteBehavior` enum as a parameter:

- `Cascade` - dependents should be deleted
- `Restrict` - dependents are unaffected
- `SetNull` - the foreign key values in dependent rows should update to NULL

If the database was created from EF Core migrations, the specified behaviour is set up automatically. 

This example features a relationship that is defined purely by the presence of a single navigational property (`Books` in the `Author` entity):
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
}
```
Because there is no foreign key property in the dependent `Book` entity, EF Core will configure a nullable `AuthorId` foreign key column in the Books table with a referential constraint action of `Restrict`, which will translate to `NO ACTION` in SQL Server. The following example illustrates how to override that so that the foreign key field is set to null when the principal is deleted:
```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Book>()
        .HasOne(b => b.Author)
        .WithMany(a => a.Books)
        .OnDelete(DeleteBehavior.SetNull);
}
```
The next example illustrates how to configure the relationship as required and to specify that dependent rows are deleted when the principal is deleted:
```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Book>()
        .HasOne(b => b.Author)
        .WithMany(a => a.Books)
        .IsRequired()
        .OnDelete(DeleteBehavior.Cascade);
}
```


In the following example, the conventional behaviour will be to cascade delete operations to dependents because the relationship is _required_ (based on the presence of a non-nullable foreign key property in the dependent `Tenant` entity):

```csharp
public class Property
{
    public int PropertyId { get; set; }
    public string Address { get; set; }
    public ICollection<Tenant> Tenants { get; set; }
}
public class Tenant
{
    public int TenantId { get; set; }
    public string Name { get; set; }
    public int PropertyId { get; set; }
    public Property Property { get; set; }
}
```
This behaviour can be overridden  using the Fluent API so that the `PropertyId` column in the Tenants table is set to NULL in the event that a related property is deleted as follows:
```csharp
protected override void  OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Property>()
        .HasMany(p => p.Tenants)
        .WithOne(t => t.Property)
        .OnDelete(DeleteBehavior.SetNull);
}
```

#### Data Annotation Attributes

There is no Data Annotation equivalent to the OnDelete method.

#### Further Reading
- [Configuring One To Many Relationships](/configuration/one-to-many-relationship-configuration)
