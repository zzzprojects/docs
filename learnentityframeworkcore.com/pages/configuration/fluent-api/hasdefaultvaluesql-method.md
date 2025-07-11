---
title: The Fluent API HasDefaultValueSql Method
description: Usage of the Fluent API HasDefaultValueSql Method in Entity Framework Core
canonical: /configuration/fluent-api/hasdefaultvaluesql-method
status: Published
lastmod: 2025-07-11
---

# EF Core HasDefaultValueSql

The Entity Framework Core Fluent API `HasDefaultValueSql` method is used to specify the expression used to generate the default value for a database column mapped to a property. 
```csharp
public class Contact
{
    public int ContactId { get; set; }
    public string FirstName { get; set; }
    public string LastName { get; set; }
    public string Email { get; set; }
    public bool IsActive { get; set; }
    public DateTime DateCreated { get; set; }
}

public class SampleContext : DbContext
{
    public DbSet<Contact> Contacts { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Contact>()
            .Property(p => p.DateCreated)
            .HasDefaultValueSql("GetUtcDate()");
    }
}
```

### Data Annotations
It is not possible to configure default database column values using data annotations.
