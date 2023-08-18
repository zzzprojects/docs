---
title: The Fluent API HasAlternateKey Method
description: Usage of the Fluent API HasAlternateKey Method in Entity Framework Core
canonical: /configuration/fluent-api/hasalternatekey-method
status: Published
lastmod: 2023-02-26
---

# EF Core HasAlternateKey


The Entity Framework Core Fluent API `HasAlternateKey` method enables you to create an alternate key by placing a unique constraint (and therefore a unique index) on a property or properties other than those that form the primary key. This is typically done to help ensure the uniqueness of data.
```csharp
public class SampleContext : DbContext
{
    public DbSet<Applicant> Applicants { get; set; }
    
    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Applicant>()
            .HasAlternateKey(a => a.PassportNumber);
    }      
}

public class Applicant
{
    public int ApplicantId { get; set; }
    public string Name { get; set; }
    public string PassportNumber { get; set; }
}
```
If more than one property takes part in the unique constraint (composite alternate key), they are specified as properties of an anonymous type:

```csharp
public class SampleContext : DbContext
{
    public DbSet<Applicant> Applicants { get; set; }
    
    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Applicant>()
            .HasAlternateKey(a => new { a.PassportNumber, a.CountryCode });
    } 
}

public class Applicant
{
    public int ApplicantId { get; set; }
    public string Name { get; set; }
    public string PassportNumber { get; set; }
    public string CountryCode { get; set; }
}
```

### Data Annotations

There is no Data Annotation equivalent to the `HasAlternateKey` method.
