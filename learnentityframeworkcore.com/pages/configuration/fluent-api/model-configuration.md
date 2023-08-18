---
title: Entity Framework Core Model Configuration
description: You can configure a number of aspects of the model via the Entity Framework Core Fluent API. These options are made available through methods on the ModelBuilder type.
canonical: /configuration/fluent-api/model-configuration
status: Published
lastmod: 2023-02-27
---

# EF Core Mapping 

You can configure a number of aspects of the model via the Entity Framework Core Fluent API. These options are made available through methods on the `ModelBuilder` type.

### Schema

The default schema that EF Core uses to create database objects is `dbo`. You can change this behaviour using the `ModelBuilder`'s `HasDefaultSchema` method:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.HasDefaultSchema("MyCustomSchema");
}
```
#### Data Annotations
It is not possible to specify the default schema using Data Annotations attributes.

### Exclude a Type 
The `Ignore<TEntity>()` method enables you to explicitly exclude an entity from the EF model, and therefore it will not be mapped to a database table. 

In the following example, the `AuditLog` entity is referenced as a navigation property in the `Contact` entity, and will be included in the model by convention. The `Ignore` method is used to ensure that the `AuditLog` class will not be mapped to a table in the database:

```csharp
public class SampleContext : DbContext
{
    public DbSet<Contact> Contacts { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Ignore<AuditLog>();
    }
}

public class Contact
{
    public int Id { get; set; }
    public string FullName { get; set; }
    public string Email { get; set; } 
    public AuditLog AuditLog { get; set; }
}

public class AuditLog
{
    public int EntityId { get; set; }
    public int UserId { get; set; }
    public DateTime Modified { get; set; }
}
```
#### Data Annotations
The Data Annotations equivalent to the `Ignore` method is the [NotMapped attribute](/configuration/data-annotation-attributes/notmapped-attribute).
