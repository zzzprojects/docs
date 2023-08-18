---
title: Table Per Hierarchy Inheritance
description: EF Core support for inheritance uses the Table Per Hierarchy pattern
canonical: /inheritance/table-per-hierarchy
proficiencylevel: Intermediate
status: Published
lastmod: 2023-08-16
---

# EF Core TPH

Entity Framework Core will represent an object-oriented hierarchy in a single table that takes the name of the base class and includes a "discriminator" column to identify the specific type for each row.  EF Core will only include types that are explicitly included in the model and will only implement the discriminator column if two or more types (including the base type) have a `DbSet` configured for them.

## The Model

Here is the model being used to illustrate inheritance in EF Core. It represents the type of contracts that you might enter into with a media/communications business.

```csharp
public class Contract
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

public class TvContract : Contract
{
    public PackageType PackageType { get; set; }
}

public class BroadBandContract : Contract
{
    public int DownloadSpeed { get; set; }
}

public enum PackageType
{
    S, M, L, XL
}
```

## Convention

Entity Framework Core offers two approaches to configuring the model so that the framework recognizes that an inheritance hierarchy exists. The first is to explicitly include `DbSet` properties in the `DbContext` for the base class and each of the derived types:

```csharp
public class SampleContext : DbContext
{
    public DbSet<Contract> Contracts { get; set; }
    public DbSet<MobileContract> MobileContracts { get; set; }
    public DbSet<TvContract> TvContracts { get; set; }
    public DbSet<BroadBandContract> BroadBandContracts { get; set; }
}
```
This approach has the benefit of enabling you to easily query contract types explicitly e.g.

```csharp
var mobileContracts = context.MobileContracts.ToList();
```
An alternative is to include a `DbSet` for the base class and then implicitly include the derived entities in the `OnModelCreating` method:
```csharp
public class SampleContext : DbContext
{
    public DbSet<Contract> Contracts { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<MobileContract>();
        modelBuilder.Entity<TvContract>();
        modelBuilder.Entity<BroadBandContract>();
    }
}
```
If you adopt this approach, you will need to use the `OfType<TEntity>` method to query for contracts of a particular type:

```csharp
var tvContracts = context.Contracts.OfType<TvContract>().ToList();
```

## Abstract base class
If instances of the base class are not intended to be instantiated, it would be more proper to declare the base class as `abstract` and to configure that in the `OnModelCreating` method while adding `DbSet` properties to the `DbContext` for the derived classes:

```csharp
public class SampleContext : DbContext
{
    public DbSet<MobileContract> MobileContracts { get; set; }
    public DbSet<TvContract> TvContracts { get; set; }
    public DbSet<BroadbandContract> BroadbandContracts { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Contract>().ToTable("Contracts");
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

public class TvContract : Contract
{
    public PackageType PackageType { get; set; }
}

public class BroadbandContract : Contract
{
    public int DownloadSpeed { get; set; }
}

public enum PackageType
{
    S, M, L, XL
}
```

Each of the approaches outlined above will result in the same table structure being created:

![Entity Framework Core Table Per Hierarchy](/images/13-09-2016-14-49-54.png)

The table includes fields for the properties of the base `Contract` class, and for the properties in the derived classes. It also includes a column named "Discriminator", designed to identify the derived type that the data row represents. By default, EF Core will use the name of the class as a value for the discriminator column. So the value for a row that contains e.g. a TvContract will be "TvContract".

## Configuration

You can configure aspects of the inheritance mapping via the Fluent API. The example above shows how to configure the table name for the abstract base class that didn't have a `DbSet` included in the `DbContext` using the [ToTable method](/configuration/fluent-api/totable-method). In addition, you can specify a name and data type for the discriminator column, and the values to represent each type:
```csharp 
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Contract>()
            .ToTable("Contracts")
            .HasDiscriminator<int>("ContractType")
            .HasValue<MobileContract>(1)
            .HasValue<TvContract>(2)
            .HasValue<BroadbandContract>(3);
}
```
In this example, the data type for the discriminator column is specified as an `int`, and named "ContractType". If you choose to provide your values for the discriminator column, you must provide values for **all** non-abstract entities in the mapped hierarchy. The `Contract` entity in this example is abstract so it hasn't been included. But if the base type is not defined as `abstract`, it must have a discriminator value provided. 

## FAQ

<div itemscope itemtype="https://schema.org/FAQPage">

<details itemscope itemprop="mainEntity" itemtype="https://schema.org/Question">
<summary id="what-is-the-discriminator-column-when-using-a-tph-inheritance-in-ef-core" itemprop="name">What is the discriminator column when using a TPH inheritance in EF Core?</summary>
<div itemscope itemprop="acceptedAnswer" itemtype="https://schema.org/Answer"><div itemprop="text">

The discriminator column in the `TPH` inheritance is a special column added to the table to distinguish between different entity types.

Since a `TPH` inheritance stores all entity types in the same table, EF Core uses this column to identify which row corresponds to which entity type.

</div></div>
</details>

<details itemscope itemprop="mainEntity" itemtype="https://schema.org/Question">
<summary id="how-to--map-the-discrimninator-column-to-an-enum-when-using-a-tph-inheritance-in-ef-core" itemprop="name">How to map the discriminator column to an enum when using a TPH inheritance in EF Core?</summary>
<div itemscope itemprop="acceptedAnswer" itemtype="https://schema.org/Answer"><div itemprop="text">

You can map a discriminator column to an enum type by first creating the enum and then mapping the `TPH` inheritance by using the enum type as a generic parameter to the `HasDiscriminator` method. 

Here is an example:

```csharp
public enum AnimalType
{
    Cat,
    Dog
}

protected override void OnModelCreating(ModelBuilder modelBuilder)
{
	modelBuilder.Entity<Animal>()
		.HasDiscriminator<AnimalType>("DiscriminatorColumnName")
		.HasValue<Cat>("Cat")
		.HasValue<Dog>("Dog");
}
```
</div></div>
</details>


<details itemscope itemprop="mainEntity" itemtype="https://schema.org/Question">
<summary id="how-to-change-the-name-of-the-discriminator-column-when-using-a-tph-inheritance-in-ef-core" itemprop="name">How to change the name of the discriminator column when using a TPH inheritance in EF Core?</summary>
<div itemscope itemprop="acceptedAnswer" itemtype="https://schema.org/Answer"><div itemprop="text">

You can choose the discriminator column name in the `OnModelCreating` by calling the `HasDiscriminator<string>([DiscriminatorColumnName])` method when mapping your entity type.

Here is an example:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
	modelBuilder.Entity<Animal>()
		.HasDiscriminator<string>("DiscriminatorColumnName")
		.HasValue<Cat>("Cat")
		.HasValue<Dog>("Dog");
}
```
</div></div>
</details>


<details itemscope itemprop="mainEntity" itemtype="https://schema.org/Question">
<summary id="how-to-map-entities-in-the-onmodelcreating-method-to-implement-a-tph-inheritance-in-ef-core" itemprop="name">How to map entities in the 'OnModelCreating' method to implement a TPH inheritance in EF Core?</summary>
<div itemscope itemprop="acceptedAnswer" itemtype="https://schema.org/Answer"><div itemprop="text">

To implement the TPH inheritance strategy in EF Core, you need to:

1. Call the `HasDiscriminator<type>([DiscriminatorName])` method for your root entity
2. Call the `HasValue([DiscriminatorName])` method for every concrete entity

Here is an example:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
	modelBuilder.Entity<Animal>()
		.HasDiscriminator<string>("DiscriminatorColumnName")
		.HasValue<Cat>("Cat")
		.HasValue<Dog>("Dog");
}
```

In this configuration:

- The `Animal` is an abstract class, meaning you don't map it to a table. 
- The `.HasDiscriminator<string>("DiscriminatorColumnName")` will create a column name named `DiscriminatorColumnName` of type `string`.
- The `HasValue<Cat>("Cat")` map the `Cat` type to the discriminator value `Cat`.
- The `HasValue<Dog>("Dog")` map the `Dog` type to the discriminator value `Dog`.

</div></div>
</details>


<details itemscope itemprop="mainEntity" itemtype="https://schema.org/Question">
<summary id="is-there-any-performance-issue-related-to-tph-inheritance-in-ef-core" itemprop="name">Is there any performance issue related to TPH inheritance in EF Core?</summary>
<div itemscope itemprop="acceptedAnswer" itemtype="https://schema.org/Answer"><div itemprop="text">

The `TPH` inheritance generally offers good performance for read operations due to fewer joins and a simpler querying model than other inheritance. Write operations are also usually very fast since only 1 table is used.

</div></div>
</details>


<details itemscope itemprop="mainEntity" itemtype="https://schema.org/Question">
<summary id="can-i-have-multiple-dbset-instance-for-each-class-within-a-tph-inheritance-in-ef-core" itemprop="name">Can I have multiple DbSet instances for each class within a TPH inheritance in EF Core?</summary>
<div itemscope itemprop="acceptedAnswer" itemtype="https://schema.org/Answer"><div itemprop="text">

Yes, every concrete and abstract type can be in its own `DbSet`. Here is an example:

```csharp
public DbSet<Animal> Animals { get; set; }
public DbSet<Cat> Cats { get; set; }
public DbSet<Dog> Dogs { get; set; }
```

In this example, we have mapped the abstract `Animal` type to the `DbSet<Animal>` and both concrete types to `DbSet<Cat>` and `DbSet<Dog>`.

When you query the `DbSet<Animal>`, the result will be a list of `Animal`, which will be either of `Cat` or `Dog` type.

</div></div>
</details>


<details itemscope itemprop="mainEntity" itemtype="https://schema.org/Question">
<summary id="can-i-combine-tph-inhertiance-with-another-inheritance-in-ef-core" itemprop="name">Can I combine TPH inheritance with another inheritance in EF Core?</summary>
<div itemscope itemprop="acceptedAnswer" itemtype="https://schema.org/Answer"><div itemprop="text">

No, you can only have one inheritance at a time. You cannot combine Table Per Hierarchy (TPC) with a [TPC](/inheritance/table-per-concrete) or [TPT](/inheritance/table-per-type) inheritance.

</div></div>
</details>


</div>