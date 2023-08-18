---
title: The Fluent API ValueGeneratedNever Method
description: Usage of the Fluent API ValueGeneratedNever Method in Entity Framework Core
canonical: /configuration/fluent-api/valuegeneratednever-method
status: Published
lastmod: 2023-02-27
---

# EF Core ValueGeneratedNever

The Entity Framework Core Fluent API `ValueGeneratedNever` provides a way to specify that the value for the selected property should never be generated automtically by the database. This is useful if you want to circumvent the database's default behaviour.

```csharp
public class SampleContext : DbContext
{
    public DbSet<Contact> Contacts { get; set; }
    
    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Car>()
            .Property(p => p.Id)
            .ValueGeneratedNever();
    } 

    public class Car
    {
        public int Id { get; set; }
        public string Model { get; set; }
        public string Email { get; set; } 
        public DateTime DateCreated { get; set; }
    }
}
``` 
Entity Framework Core does not implement a value generation strategy. Database providers differ in the way that values are automatically generated. Some will generated values for selected data types such as `Identity`, `rowversion`, `GUID`. Others may require manual configuration such as setting default values or triggers. 

### Data Annotations

The Data Annotations attribute equivalent of the `ValueGeneratedNever` method is the [`DatabaseGenerated` attribute](/configuration/data-annotation-attributes/databasegenerated-attribute) with the `None` option.

### Previous Versions
In previous versions of Entity Framework, you would use the `HasDatabaseGenerated` method with the `DatabaseGenerated.None` enumeration to achieve the same outcome as the Entity Framework core `ValueGeneratedNever` method.
