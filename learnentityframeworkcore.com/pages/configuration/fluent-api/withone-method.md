---
title: The Fluent API WithOne Method
description: Usage of the Fluent API WithOne Method in Entity Framework Core
canonical: /configuration/fluent-api/withone-method
status: Published
lastmod: 2025-07-11
---

# EF Core WithOne

The Entity Framework Core Fluent API `WithOne` method is used to configure the _one_ side of a one-to-many or one-to-one relationship. 

The `WithOne` method must be used in conjunction with the `HasOne` (one-to-one) or `HasMany` (one-to-many) method to fully configure a valid relationship, adhering to the [Has/With pattern](/configuration/fluent-api/haswith-pattern) for relationship configuration.

The following model represents companies and employees with an [inverse navigation property defined](/conventions/one-to-many-relationship#inverse-navigation-property) in the dependent entity (`Employee`):

## One-To-Many Example
```csharp
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

A company _has many_ employees, each _with one_ company. That relationship is represented as follows:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Employee>()
        .HasOne(e => e.Company)
        .WithMany(c => c.Employees);
        
}
```

### Data Annotations

Relationships can be defined with the [ForeignKey](/configuration/data-annotation-attributes/foreignkey-attribute) and [InverseProperty](/configuration/data-annotation-attributes/inverseproperty-attribute) attributes.

#### Further Reading
- [Relationships](/relationships)
- [Configuring One To Many Relationships](/configuration/one-to-many-relationship-configuration)
- [Configuring One To One Relationships](/configuration/one-to-one-relationship-configuration)
