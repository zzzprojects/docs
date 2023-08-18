---
title: The Fluent API HasMany Method
description: Usage of the Fluent API HasMany Method in Entity Framework Core
canonical: /configuration/fluent-api/hasmany-method
status: Published
lastmod: 2023-02-27
---

# EF Core HasMany

The Entity Framework Core Fluent API `HasMany` method is used to configure the _many_ side of a one-to-many relationship. The `HasMany` method must be used in conjunction with the `HasOne` method to fully configure a valid relationship, adhering to the [Has/With pattern](/configuration/fluent-api/haswith-pattern) for relationship configuration.



The following model represents companies and employees with an [inverse navigation property defined](/configuration/one-to-many-relationship-configuration#inverse-navigation-property) in the dependent entity (`Employee`):

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
    modelBuilder.Entity<Company>()
        .HasMany(c => c.Employees)
        .WithOne(e => e.Company);
}
```
### Data Annotations

Relationships can be defined with the [ForeignKey](/configuration/data-annotation-attributes/foreignkey-attribute) and [InverseProperty](/configuration/data-annotation-attributes/inverseproperty-attribute) attributes.

#### Further Reading
- [Relationships](/relationships)
- [Configuring One To Many Relationships](/configuration/one-to-many-relationship-configuration)
- [Configuring One To One Relationships](/configuration/one-to-one-relationship-configuration)


