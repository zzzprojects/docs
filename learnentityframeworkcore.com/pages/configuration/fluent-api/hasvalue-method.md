---
title: The Fluent API HasValue Method
description: Usage of the Fluent API HasValue Method in Entity Framework Core
canonical: /configuration/fluent-api/hasvalue-method
status: Published
lastmod: 2023-02-27
---

# EF Core HasValue

The Entity Framework Core Fluent API `HasValue` method is used to configure discriminator values for entities in an [inheritance hierarchy](/inheritance) when a [table per hierarchy](/inheritance/table-per-hierarchy) approach is chosen for mapping inheritance to a database.

The `HasValue` method is available on the `DiscriminatorBuilder` type, which is returned when the [HasDiscriminator](/configuration/fluent-api/hasdiscriminator-method) method is called. It takes a type parameter specifying the type that the value applies to, and an object representing the value to apply to the specified type. In the following example, the discriminator values for the `MobileContract` type and the `BroadbandContract` type are configured as 1 and 2 respectively.

```csharp
public class SampleContext : DbContext
{
    public DbSet<MobileContract> MobileContracts { get; set; }
    public DbSet<TvContract> TvContracts { get; set; }
    public DbSet<BroadbandContract> BroadbandContracts { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Contract>()
            .ToTable("Contracts")
            .HasDiscriminator<int>("ContractType")
            .HasValue<MobileContract>(1)
            .HasValue<BroadbandContract>(2);
    }
}
    
public abstract class Contract
{
    public int ContractId { get; set; }
    public DateTime StartDate { get; set; }
    public int Months { get; set;}
    public decimal Charge { get; set; }
}

public class MobileContract : Contract
{
    public string MobileNumber { get; set; }
}

public class BroadbandContract : Contract
{
    public int DownloadSpeed { get; set; }
}
```

### Data Annotations
It is not possible to configure discriminator values with Data Annotation attributes.

#### Further Reading
- [Inheritance](/inheritance)
- [Table Per Hierarchy](/inheritance/table-per-hierarchy)
- [HasDiscriminator Method](/configuration/fluent-api/hasdiscriminator-method)
