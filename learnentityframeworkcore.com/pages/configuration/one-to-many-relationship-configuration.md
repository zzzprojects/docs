---
title: Configuring one-to-many Relationship in Entity Framework Core
description: How to use the Fluent API to configure one-to-many relationships in Entity Framework Core
canonical: /configuration/one-to-many-relationship-configuration
status: Published
lastmod: 2023-02-25
---

# EF Core One To Many Relationship

Most one-to-many relationships in an Entity Framework Core model [follow conventions](/conventions/one-to-many-relationship) and require no additional configuration. Where the model does not follow convention, the Fluent API can be used to configure the correct relationship between entities.

## Has/With pattern

When configuring relationships with the Fluent API, you will use the [_Has/With_ pattern](/configuration/fluent-api/haswith-pattern). The _Has_ side of the pattern is represented by the `HasOne` and `HasMany` methods. The _With_ side of the relationship is represented by the `WithOne` and `WithMany` methods.

The following model represents companies and employees with an [inverse navigation property defined](/conventions/one-to-many-relationship#inverse-navigation-property) in the dependent entity (`Employee`) but no matching foreign key property in the dependent:

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

A company has many employees, each with one company. That relationship is represented as follows:

```csharp
protected override void OnModelCreating(Modelbuilder modelBuilder)
{
    modelBuilder.Entity<Company>()
        .HasMany(c => c.Employees)
        .WithOne(e => e.Company);
}
```
It can also be configured by starting with the other end of the relationship:

```csharp
protected override void OnModelCreating(Modelbuilder modelBuilder)
{
    modelBuilder.Entity<Employee>()
        .HasOne(e => e.Company)
        .WithMany(c => c.Employees);
}
```
Whether starting with the `Company` or `Employee` entity, this configuration will result in an _optional_ relationship. A foreign key [shadow property](/model/shadow-properties) named `CompanyId` will be introduced by EF Core to the `Employee` entity which will be nullable. In addition, the [referential action constraint](/relationships/referential-constraint-action-options) on the relationship will be set to `NoAction`. 

## Required relationship

You can use the `IsRequired` method on the relationship to prevent the relationship from being optional:

```csharp
protected override void OnModelCreating(Modelbuilder modelBuilder)
{
    modelBuilder.Entity<Company>()
        .HasMany(c => c.Employees)
        .WithOne(e => e.Company).
        .IsRequired();
}
```
The [referential action constraint](/relationships/referential-constraint-action-options) on the relationship will be set to `Cascade`. 


## Cascading Referential Integrity Constraints

If the relationship is configured as optional, the default behavior of EF Core is to take no action in respect of the dependent entity if the principal is deleted. Consequently, if you delete the principal, any dependents will be left with foreign key values that reference principals that no longer exist. The default behavior of a database is to raise an error in this scenario: foreign key values must reference existing primary key values. 

You can alter this behavior through the `OnDelete` method which takes a `DeleteBehaviour` enumeration. The following example sets the foreign key value of the dependent entity to `null` in the event that the principal is deleted:

```csharp
protected override void OnModelCreating(Modelbuilder modelBuilder)
{
    modelBuilder.Entity<Company>()
        .HasMany(c => c.Employees)
        .WithOne(e => e.Company).
        .OnDelete(DeleteBehavior.SetNull);
}
```
This example will result in the dependent entity being deleted:
```csharp
protected override void OnModelCreating(Modelbuilder modelBuilder)
{
    modelBuilder.Entity<Company>()
        .HasMany(c => c.Employees)
        .WithOne(e => e.Company).
        .OnDelete(DeleteBehavior.Delete);
}
```

#### Further Reading
- [Fluent API OnDelete method](/configuration/fluent-api/ondelete-method)
- [Deleting Data](/dbcontext/deleting-data)  
 
[//]: #http://ef.readthedocs.io/en/latest/modeling/relationships.html
