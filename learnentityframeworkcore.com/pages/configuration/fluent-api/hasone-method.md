---
title: The Fluent API HasOne Method
description: Usage of the Fluent API HasOne Method in Entity Framework Core
canonical: /configuration/fluent-api/hasone-method
status: Published
lastmod: 2023-02-27
---

# EF Core HasOne

The Entity Framework Core Fluent API `HasOne` method is used to configure the one side of a one to many relationship, or one end of a one to one relationship.

In order to fully configure a valid relationship, it is necessary to follow the [Has/With pattern](/configuration/fluent-api/haswith-pattern) and pair the use of `HasOne` with either the `WithOne` or `WithMany` method, depending on whether the relationship being configured is a one-to-one relationship or a one-to-many relationship. 

### One To One Relationship

The following example illustrates the use of `HasOne` with `WithOne` to configure a one-to-one relationship:
```csharp
public class SampleContext : DbContext
{
    public DbSet<Author> Authors { get; set; }
    
    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Author>()
            .HasOne(a => a.Biography)
            .WithOne(b => b.Author);
    }
}

public class Author
{
    public int AuthorId { get; set; }
    public string FirstName { get; set; }
    public string LastName { get; set; }
    public AuthorBiography Biography { get; set; }
}

public class AuthorBiography
{
    public int AuthorBiographyId { get; set; }
    public string Biography { get; set; }
    public int AuthorId { get; set; }
    public Author Author { get; set; }
}
```
### One To Many Relationship
This example illustrates the use of the `HasOne` method together with the `WithMany` method to configure a one-to-many relationship:


```csharp
public class SampleContext : DbContext
{
    public DbSet<Employee> Employees { get; set; }
    public DbSet<Company> Companies { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Employee>()
        .HasOne(e => e.Company)
        .WithMany(c => c.Employees);
    }

public class Company
{
    public int Id { get; set; }
    public string Name { get; set; }
    public ICollection<Employee> Employees { get; set; }
}

public class Employee
{
    public int Id { get; set; }
    public string Name { get; set; }
    public Company Company { get; set; }
}
```
### Data Annotations

Relationships can be defined with the [ForeignKey](/configuration/data-annotation-attributes/foreignkey-attribute) and [InverseProperty](/configuration/data-annotation-attributes/inverseproperty-attribute) attributes.

#### Further Reading
- [Relationships](/relationships)
- [Configuring One To Many Relationships](/configuration/one-to-many-relationship-configuration)
- [Configuring One To One Relationships](/configuration/one-to-one-relationship-configuration)
