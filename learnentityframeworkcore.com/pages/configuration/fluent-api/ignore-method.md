---
title: The Fluent API Ignore Method
description: Usage of the Fluent API Ignore Method in Entity Framework Core
canonical: /configuration/fluent-api/ignore-method
status: Published
lastmod: 2023-02-27
---

# EF Core Ignore

The Entity Framework Core Fluent API provides two `Ignore` methods. One  belongs to the `ModelBuilder` class and is used to specify that the [entity should not be mapped to a database table](/configuration/fluent-api/model-configuration#ignore). The other `Ignore` method is available on the `EntityTypeBuilder` class and enables you to exclude individual properties from mapping.

## EF Core Ignore Entity
By convention, the `AuditLog` entity will be included in the model because it is referenced through a navigation property in the Contact entity. The `Ignore` method is used in the `OnModelCreating` method to specify that it is to be excluded from mapping in the example below:
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
    public int ContactId { get; set; }
    public string FirstName { get; set; }
    public string LastName { get; set; }
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

## EF Core Ignore Property

The `Ignore` method is usd to specify that the auto-implemented `FullName` property in the `Contact` class below is excluded from mapping:

```csharp
public class SampleContext : DbContext
{
    public DbSet<Contact> Contacts { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Contact>().Ignore(c => c.FullName);
    }
}

public class Contact
{
    public int ContactId { get; set; }
    public string FirstName { get; set; }
    public string LastName { get; set; }
    public string FullName => $"{FirstName} {LastName}";
    public string Email { get; set; } 
}
```



### Data Annotations
The Data Annotations equivalent to the `Ignore` method is the [NotMapped attribute](/configuration/data-annotation-attributes/notmapped-attribute).
