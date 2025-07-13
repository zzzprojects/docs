---
title: Inheritance in Entity Framework Core
description: Inheritance is a key object-oriented programming concept that facilitates code reuse and is often used to model hierarchies. It is supported in Entity Framework Core using the Table Per Hierarchy pattern
canonical: /inheritance
status: Published
lastmod: 2025-07-13
---

# EF Core Inheritance 

Inheritance is a key object-oriented programming concept that facilitates code reuse and is often used to model hierarchies. The following model represents various types of contracts that you might be able to enter into with a media/communications business:

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

The base type is `Contract`. Specific types of contracts inherit from the base class: a mobile phone contract, a TV contract, and a broadband contract. Each of the types of contract shares the `ContractId`, `StartDate`, `Months`, and `Charge` properties with their base type through inheritance, and implements additional type-specific properties.

There are three ways in which object-oriented inheritance can be represented in a database:

## Table Per Hierarchy (TPH)

One table is used to represent all classes in the hierarchy. A "discriminator" column is used to discriminate between differing types. The table takes the name of the base class or its associated `DbSet` property by default. 

In previous versions of Entity Framework, TPH was the default mapping pattern for inheritance. Entity Framework Core currently only [implements the TPH pattern](/inheritance/table-per-hierarchy). 

## Table Per Type (TPT)

A separate table is used to represent each type in the inheritance chain, including abstract types. Tables that represent derived types are related to their base type via foreign keys.  

EF Core 5.0 [implements the TPT pattern](/inheritance/table-per-type) that allows mapping each .NET type in an inheritance hierarchy to a different database table known as table-per-type (TPT) mapping.

## Table Per Concrete Type (TPC)

A separate table is used to represent each _concrete_ type in the inheritance chain. Properties in any abstract base type are generated as fields in each concrete type's table. There is no relationship between differing types.

<!--http://weblogs.asp.net/manavi/inheritance-mapping-strategies-with-entity-framework-code-first-ctp5-part-3-table-per-concrete-type-tpc-and-choosing-strategy-guidelines-->

The [TPC pattern](/inheritance/table-per-concrete) feature was introduced in EF Core 7.0. 

#### Further Reading

- [Table Per Hierarchy](/inheritance/table-per-hierarchy)
- [Table Per Type](/inheritance/table-per-type)
- [Table Per Concrete](/inheritance/table-per-concrete)
