---
title: Fluent API Configuration
description: How to use the Entity Framework Core Fluent API to configure aspects of the model
canonical: /configuration/fluent-api
status: Published
lastmod: 2025-07-11
---

# EF Core Fluent API

EF Core's Fluent API provides methods for configuring various aspects of your model:

- [Model-wide configuration](/configuration/fluent-api/model-configuration)
- [Type configuration](/configuration/fluent-api/type-configuration)
- [Property configuration](/configuration/fluent-api/property-configuration)

Configurations are applied via a number of methods exposed by the `Microsoft.EntityFrameworkCore.ModelBuilder` class. The `DbContext` class has a method called `OnModelCreating` that takes an instance of `ModelBuilder` as a parameter.  This method is called by the framework when your context is first created to build the model and its mappings in memory. You can override this method to add your own configurations:

```csharp
public class SampleContext : DbContext
{
    // Specify DbSet properties etc

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        // add your own configuration here
    }
}
```

The term _Fluent API_ refers to a pattern of programming where method calls are chained together with the end result being certainly less verbose and arguably more readable than a series of statements:

```csharp
// series of statements
modelBuilder.Entity<Order>().Property(t => t.OrderDate).IsRequired();
modelBuilder.Entity<Order>().Property(t => t.OrderDate).HasColumnType("Date");
modelBuilder.Entity<Order>().Property(t => t.OrderDate).HasDefaultValueSql("GetDate()");

// fluent api chained calls
modelBuilder.Entity<Order>()
    .Property(t => t.OrderDate)
        .IsRequired()
        .HasColumnType("Date")
        .HasDefaultValueSql("GetDate()");
```
In this example, the `DateCreated` property of the `Order` entity is configured as `Required` (not nullable), is mapped to a SQL Server `Date` type and has a default value applied. Each method call returns a type that exposes its own set of methods which is what enables the chaining of methods. Often, the type returned from the method call is the same instance as the one on which the method is called. For example, the `IsRequired` method above is exposed by the `PropertyBuilder` class. The return type of this method is an instance of the `PropertyBuilder` class, so once the method has performed its task, it ends simply with `return this;`.

The Fluent API provides a larger range of configuration options than Data Annotation attributes. Fluent API configuration also facilitates cleaner code, in that the configuration can be kept separate from the domain classes. 

## Separate Configuration Classes

Most examples in this guide show configurations being applied in the `OnModelCreating` method, but it is recommended to separate configurations out to individual files per entity - especially for larger models or ones that require a lot of configuration. 

In pre-.NET Core versions of Entity Framework, this is achieved by creating classes that derive from `EntityTypeConfiguration<TEntity>`, and then using Fluent API to override conventions in the class constructor. These classes are then added to the `DbModelBuilder`'s configuration in the `OnModelCreating` method. Prior to version 2.0 of Entity Framework Core, there was no equivalent to this approach and one had to roll one's own solution.

In version 2.0, a new interface was introduced: `IEntityTypeConfiguration<TEntity>`. This is used in a similar way to `EntityTypeConfiguration<TEntity>` in that configurations are specified in separate entity-specific classes:

```csharp
public class OrderConfiguration : IEntityTypeConfiguration<Order>
{
    public void Configure(EntityTypeBuilder<Order> builder)
    {
	builder.HasKey(o => o.OrderNumber);
	builder.Property(t => t.OrderDate)
            .IsRequired()
            .HasColumnType("Date")
            .HasDefaultValueSql("GetDate()");
    }
}
```

This configuration is applied in the `OnModelCreating` method as follows:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.ApplyConfiguration(new OrderConfiguration());
}
```
Note that while this approach follows a similar pattern to Entity Framework 6, there are some differences:

- The configuration class must implement `IEntityTypeConfiguration<TEntity>` (an interface) rather than `EntityTypeConfiguration<TEntity>` (a class).
- The configuration is applied in a `Configure` method rather than in a constructor.
- Configurations are added to the `ModelBuilder` using an `ApplyConfiguration` method instead of being added to a `Configurations` collection. 

In the example above, only one entity type configuration was registered. Larger applications will require multiple type configurations, and as the scope of the application grows, the developer will have to remember to register all new type configurations. A new extension method, `ApplyConfigurationsFromAssembly `,  was introduced in 2.2, which scans a given assembly for all types that implement `IEntityTypeConfiguration`, and registers each one automatically. It is used like this:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.ApplyConfigurationsFromAssembly(Assembly.GetExecutingAssembly());
}
```
Once this line of code has been added, you no longer need to remember to add new type configuration registrations to the `OnModelCreating` method as your model grows.