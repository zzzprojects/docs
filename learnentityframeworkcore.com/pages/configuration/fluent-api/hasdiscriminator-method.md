---
title: The Fluent API HasDiscriminator Method
description: Usage of the Fluent API HasDiscriminator Method in Entity Framework Core
canonical: /configuration/fluent-api/hasdiscriminator-method
status: Published
lastmod: 2023-02-26
---

# EF Core HasDiscriminator

The Entity Framework Core Fluent API `HasDiscriminator` method is used to configure aspects of the discriminator column in a [table that represents an inheritance hierarchy](/inheritance/table-per-hierarchy).

By convention, a discriminator column will be configured to use a string data type will be named "Discriminator". The `HasDiscriminator` method takes a type parameter reprsenting the data type for the column, and a string representing the name for the column. In the following example, the table used to store instances of entities that inherit from the abstract `Contract` entity will feature a discriminator column named "ContractType":

```csharp 
public class SampleContext : DbContext
{
    public DbSet<MobileContract> MobileContracts { get; set; }
    public DbSet<BroadbandContract> BroadbandContracts { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Contract>()
            .HasDiscriminator<string>("ContractType");
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
By default, EF Core will use the type name as a discriminator value. If you want to use another data type, such as an integer, you must also use the `HasValue` method to provide values for **all** concrete type in the inheritance hierarchy. The following example configures the discriminator column's data type as numeric and provides default values for each type:

```csharp 
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Contract>()
        .HasDiscriminator<int>("ContractType")
        .HasValue<MobileContract>(1)
        .HasValue<BroadbandContract>(2);
}
```


### Data Annotations
No Data Annotations are available for configuring discriminator columns.

#### Further Reading
- [Inheritance](/inheritance)
- [Table Per Hierarchy](/inheritance/table-per-hierarchy)
