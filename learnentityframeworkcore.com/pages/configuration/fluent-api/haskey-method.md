---
title: The Fluent API HasKey Method
description: Usage of the Fluent API HasKey Method in Entity Framework Core
canonical: /configuration/fluent-api/haskey-method
status: Published
lastmod: 2023-02-27
---

# EF Core HasKey

The Entity Framework Core Fluent API `HasKey` method is used to denote the property that uniquely identifies an entity (the `EntityKey`), and which is mapped to the Primary Key field in a database:
```csharp
public class SampleContext : DbContext
{
    public DbSet<Order> Orders { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Order>()
            .HasKey(o => o.OrderNumber);
    }
}

public class Order
{
    public int OrderNumber { get; set; }
    public DateTime DateCreated { get; set; }
    public Customer Customer { get; set; }
    ...
)
```
In the example above, the name of the key field in the `Order` class does not meet the [Entity Framework Core's naming convention for entity keys](/conventions#primary-key), so the `HasKey` method is used to configure the `OrderNumber` property as the key field. 

### Composite Keys
Entity Framework Core supports composite keys - primary key values generated from two or more fields in the database. Composite keys are not covered by conventions or data annotation attributes. The only way to configure composite keys is to use the `HasKey` method. You specify the properties that form the composite key by passing them in as properties of an anonymous type to the `HasKey` method. In the following example, the composite key comprises an abbreviated version of the customer name and an integer e.g. ALFKI0001:
```csharp
public class SampleContext : DbContext
{
    public DbSet<Order> Orders { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Order>()
            .HasKey(o => new { o.CustomerAbbreviation, o.OrderNumber });
    }
}

public class Order
{
    public string CustomerAbbreviation { get; set; }
    public int OrderNumber { get; set; }
    public DateTime DateCreated { get; set; }
    public Customer Customer { get; set; }
    ...
)
```

#### Data Annotation Attributes
The Data Annotation attribute alternative to the `HasKey` method is the [Key attribute](/configuration/data-annotation-attributes/key-attribute).
