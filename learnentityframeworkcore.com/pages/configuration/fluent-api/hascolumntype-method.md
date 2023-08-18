---
title: The Fluent API HasColumnType Method
description: Meaning and usage of the Fluent API HasColumnType Method in Entity Framework Core
canonical: /configuration/fluent-api/hascolumntype-method
status: Published
lastmod: 2023-02-26
---

# EF Core HasColumnType

The `HasColumnType` method is applied to a property to specify the data type of the column that the property should map to when the type differs from convention. The following example specifies that the `Title` column in the Books table is to be configured as `varchar` instead of the default `nvarchar`:

```csharp
public class SampleContext : DbContext
{
    public DbSet<Book> Books { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Book>()
            .Property(b => b.Title).HasColumnType("varchar");
    }
}

public class Book
{
    public int BookId { get; set; }
    public string Title { get; set; }
    public Author Author { get; set; }
}
```

The `HasColumnType` method is also useful when mapping columns to user defined data types. In the next example, the `StateProvince` property on the `Address` entity is mapped to the "Name" user defined data type:

```csharp
public class SampleContext : DbContext
{
    public DbSet<Address> Address { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Address>()
            .Property(a => a.StateProvince).HasColumnType("Name");
    }
}
```
This example is taken from the [AdventureWorks sample database](https://msftdbprodsamples.codeplex.com/) for SQL server provided by Microsoft.

Finally, the `HasColumnType` method can be used in instances where you might have used the EF 6 `HasPrecision` fluent API method, which has not been carried over to EF Core ([yet](https://github.com/dotnet/efcore/issues/11614)):

```csharp
public class SampleContext : DbContext
{
    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Order>()
            .Property(o => o.ExchangeRate).HasColumnType("decimal(5,3)");
    }
}
```

### Data Annotations
The data annotations approach to specifying the data type of the mapped column is to provide a value for the `TypeName` property of the [Column attribute](/configuration/data-annotation-attributes/column-attribute). 
