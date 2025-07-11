---
title: The Fluent API HasConstraintName Method
description: Usage of the Fluent API HasConstraintName Method in Entity Framework Core
canonical: /configuration/fluent-api/hasconstraintname-method
status: Published
lastmod: 2025-07-11
---

# EF Core HasConstraintName

The Entity Framework Core Fluent API `HasConstraintName` method enables you to specify the name of the foreign key constraint for a relationship. 

By default, EF Core will name a foreign key constraint according to the following pattern:

```
FK_<table name>_<principal table name>_<foreign key column name>
```
The following model will result in a foreign key constraint name of `FK_Employees_Companies_CompanyId`:

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
The foreign key name is taken from the [shadow property](/model/shadow-properties) that will be created in the absence of a foreign key property being defined in the dependent `Employee` entity.

The `HasConstraintName` method is available on the `ReferenceCollectionBuilder` type, so the relationship requires configuring in order to make the method available:
```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Company>()
        .HasMany(c => c.Employees)
        .WithOne(e => e.Company)
        .HasConstraintName("MyFKConstraint");
}
```
### Data Annotations
Constraint names cannot be configured with data annotation attributes.

