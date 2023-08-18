---
title: Mapping Attributes in Entity Framework Core
description: Mapping attributes can be used to customize the behavior of EF Core entities and properties when they are mapped to the database.
canonical: /misc/mapping-attributes
status: Published
lastmod: 2023-02-16
---

# EF Core Mapping Attributes

EF Core provides a set of mapping attributes that can be used to configure how entities and their properties are mapped to the database. Here are some of the most commonly used mapping attributes in EF Core:

 - **Key:** Marks a property as the primary key for the entity.
 - **Table:** Specifies the name of the database table that maps to an entity.
 - **Column:** Maps a property to a specific column in the database.
 - **ForeignKey:** Specifies the foreign key property for a navigation property.
 - **InverseProperty:** Specifies the navigation property on the other end of a relationship.
 - **Required:** Specifies that a property cannot be null.
 - **MaxLength:** Specifies the maximum length of a string or byte array property.
 - **DatabaseGenerated:** Specifies how a value is generated for a property in the database (e.g. Identity or Computed).
 - **NotMapped:** Excludes a property from being mapped to the database.

These attributes can be applied to entity classes and their properties to customize the way they are mapped to the database. They allow you to define relationships, specify data types, and configure database constraints. By using these attributes, you can create a mapping that is optimized for your specific scenario and ensure that your data is persisted in the most efficient way possible.

here's an example of how some of the mapping attributes can be used in EF Core:

```csharp
public class Customer
{
    [Key]
    public int Id { get; set; }

    [Required]
    [MaxLength(50)]
    public string Name { get; set; }

    [Column("EmailAddress")]
    public string Email { get; set; }

    [ForeignKey("Country")]
    public int CountryId { get; set; }
    public virtual Country Country { get; set; }
}

public class Country
{
    [Key]
    public int Id { get; set; }

    [Required]
    [MaxLength(50)]
    public string Name { get; set; }

    [InverseProperty("Country")]
    public virtual ICollection<Customer> Customers { get; set; }
}
```

In this example, we have two entities `Customer` and `Country` that are related through a foreign key. 

## New Mapping Attributes

In the latest version of EF Core, there are various new attributes are introduced that can be utilized in the code to modify the default mapping of entities with the database.

### Unicode

The Unicode attribute is used in Entity Framework Core to specify that a string property should be mapped to the non-Unicode column in the underlying database. 

 - This attribute is applied to a string property in an entity class, and it ensures that the column that corresponds to the property is created with the non-Unicode data type.
 - If the database system supports only `Unicode` types then it will ignore the `Unicode` attribute.

```csharp
public class Book
{
    public int Id { get; set; }
    public string Title { get; set; }

    [Unicode(false)]
    [MaxLength(22)]
    public string Isbn { get; set; }
}
```

In this class, the `Isbn` property is marked with the `[Unicode(false)]` attribute. When EF Core creates the database schema for this entity, it will ensure that the column corresponding to the `Isbn` property is created with the non-Unicode data type and to restrict the size of the database column, `MaxLength` is used.

```csharp
CREATE TABLE [Book] (
    [Id] int NOT NULL IDENTITY,
    [Title] nvarchar(max) NULL,
    [Isbn] varchar(22) NULL,
    CONSTRAINT [PK_Book] PRIMARY KEY ([Id]));
```

In SQL Server, this results in a database column of `varchar(22)`.

## Precision

The `Precision` attribute in Entity Framework Core is used to specify the precision of a `decimal` property in the database. The `Precision` attribute can be applied to a decimal property in an entity class and it determines the number of digits that are stored to the left and right of the decimal point.

Here is an example:

```csharp
public class Book
{
    public int Id { get; set; }
    public string Title { get; set; }

    [Unicode(false)]
    [MaxLength(22)]
    public string Isbn { get; set; }

    [Precision(18, 4)]
    public decimal Price { get; set; }
}
```

In this example, the `Price` property has been marked with the `[Precision(18, 4)]` attribute. 

 - The attribute takes two parameters: the first parameter specifies the total number of digits that can be stored in the decimal, and the second parameter specifies the number of digits that are stored to the right of the decimal point.
 - The `Precision` attribute is useful when you want to ensure that a decimal property is stored in the database with a specific level of precision. 
 - This is important when dealing with financial or scientific data where the level of precision is critical.

### EntityTypeConfiguration

The `EntityTypeConfiguration` attribute in Entity Framework Core is used to specify the configuration for an entity type. The `EntityTypeConfiguration` attribute is applied to a class that inherits from the `IEntityTypeConfiguration<TEntity>` interface.

Here is an example:

```csharp
[EntityTypeConfiguration(typeof(ProductConfiguration))]
public class Product
{
    public int Id { get; set; }
    
    public string Name { get; set; }
    
    public decimal Price { get; set; }
}
```

In this example, the `Product` class has been marked with the `[EntityTypeConfiguration(typeof(ProductConfiguration))]` attribute. The attribute takes one parameter, which is the type of the class that implements the `IEntityTypeConfiguration<TEntity>` interface for the `Product` entity.

The `ProductConfiguration` class would look like this:

```csharp
public class ProductConfiguration : IEntityTypeConfiguration<Product>
{
    public void Configure(EntityTypeBuilder<Product> builder)
    {
        builder.Property(p => p.Name)
            .HasMaxLength(50);
        
        builder.Property(p => p.Price)
            .HasColumnType("decimal(18, 4)");
    }
}
```

The `ProductConfiguration` class implements the `IEntityTypeConfiguration<TEntity>` interface for the `Product` entity. 

 - In the `Configure` method, we can specify the configuration for the `Product` entity using the `EntityTypeBuilder` class. 
 - In this example, we are setting the maximum length of the `Name` property to 50 characters and specifying the database column type for the `Price` property as a `decimal` with a maximum precision of 18 digits and a scale of 4.

Using the `[EntityTypeConfiguration]` attribute is a useful way to organize the configuration for your entities. Instead of putting all the configuration in the `DbContext` class, you can create separate configuration classes for each entity and apply the `[EntityTypeConfiguration]` attribute to the entity class to specify which configuration class to use.
