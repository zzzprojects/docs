---
title: Column in EF Core
description: Learn how to configure column names, types, order, nullability, precision, length, Unicode, and collations in EF Core using Data Annotations and Fluent API.
canonical: /mapping/column
status: Published
lastmod: 2026-11-23
---

# Column in EF Core

In a relational database, EF Core typically maps each entity property to a column in a table. By default, the column uses the same name as the property, but you can customize its name, data type, order, and other settings using Data Annotations or Fluent API.

For more details about column naming, see our [Column Attribute](/configuration/data-annotation-attributes/column-attribute) guide.

## Column Name

Use `[Column]` or `HasColumnName()` to map a property to a column with a different name.

**Data Annotations**

```csharp
public class Product
{
    public int ProductId { get; set; }

    [Column("ProductName")]
    public string Name { get; set; } = null!;
}
```

**Fluent API**

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Product>()
        .Property(p => p.Name)
        .HasColumnName("ProductName");
}
```

Both configurations map the `Name` property to the `ProductName` database column without changing the C# property name.

## Column Type

To specify the database column type, use the `TypeName` property of `[Column]` or `HasColumnType()`.

**Data Annotations**

```csharp
public class Product
{
    public int ProductId { get; set; }

    [Column("ProductName", TypeName = "varchar(100)")]
    public string Name { get; set; } = null!;
}
```

**Fluent API**

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Product>()
        .Property(p => p.Name)
        .HasColumnName("ProductName")
        .HasColumnType("varchar(100)");
}
```

The `Name` property now maps to a `ProductName` column with the SQL type `varchar(100)`. This example uses a SQL Server-compatible type; available column types depend on the database provider.

For more information about configuring database column types, see Microsoft's [Entity Properties documentation](https://learn.microsoft.com/en-us/ef/core/modeling/entity-properties#column-data-types).

## Column Order

You can specify a column's order when EF Core creates a table using the `Order` property of `[Column]` or `HasColumnOrder()`.

**Data Annotations**

```csharp
public class Product
{
    public int ProductId { get; set; }

    [Column("ProductName", TypeName = "varchar(100)", Order = 1)]
    public string Name { get; set; } = null!;
}
```

**Fluent API**

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Product>()
        .Property(p => p.Name)
        .HasColumnName("ProductName")
        .HasColumnType("varchar(100)")
        .HasColumnOrder(1);
}
```

Both configurations assign an explicit column order of `1` to `ProductName`. Column ordering starts at `0`, but the actual position also depends on how other columns are ordered.

**Note:** Column ordering applies when creating a table. Changing the configuration does not automatically reorder columns in an existing table.

## Column Comment

Use `[Comment]` or `HasComment()` to add a descriptive comment to a database column.

**Data Annotations**

```csharp
public class Product
{
    public int ProductId { get; set; }

    [Comment("The name of the product")]
    public string Name { get; set; } = null!;
}
```

**Fluent API**

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Product>()
        .Property(p => p.Name)
        .HasComment("The name of the product");
}
```

The comment documents the `Name` column in the database schema, where supported by the provider.

## NotMapped / Ignore

By convention, EF Core includes public properties with getters and setters in the model. Use `[NotMapped]` or `Ignore()` to exclude a property.

**Data Annotations**

```csharp
public class Product
{
    public int ProductId { get; set; }
    public string Name { get; set; } = null!;

    [NotMapped]
    public string DisplayName { get; set; } = null!;
}
```

**Fluent API**

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Product>()
        .Ignore(p => p.DisplayName);
}
```

In both cases, EF Core excludes `DisplayName` from the model, so no corresponding column is created.

For more details, see our [NotMapped Attribute](/configuration/data-annotation-attributes/notmapped-attribute) guide.

## Required

Use `[Required]` or `IsRequired()` to configure a property as required.

**Data Annotations**

```csharp
public class Product
{
    public int ProductId { get; set; }

    [Required]
    public string? Name { get; set; }
}
```

**Fluent API**

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Product>()
        .Property(p => p.Name)
        .IsRequired();
}
```

Both configurations make `Name` required in the EF Core model, resulting in a `NOT NULL` database column.

**Note:** With nullable reference types enabled, a non-nullable property such as `string Name` is already required by convention. Here, `string?` demonstrates explicit configuration.

For more details, see our [Required Attribute](/configuration/data-annotation-attributes/required-attribute) guide.

## Precision and Scale

Use `[Precision]` or `HasPrecision()` to configure the precision and scale of a decimal column.

**Data Annotations**

```csharp
public class Product
{
    public int ProductId { get; set; }

    [Precision(18, 2)]
    public decimal Price { get; set; }
}
```

**Fluent API**

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Product>()
        .Property(p => p.Price)
        .HasPrecision(18, 2);
}
```

The precision specifies the total number of digits, while the scale specifies the number of digits after the decimal point. On SQL Server, both configurations map `Price` to `decimal(18,2)`.

EF Core does not validate precision or scale before passing values to the database provider.

## MaxLength

Use `[MaxLength]` or `HasMaxLength()` to configure the maximum length of a string or byte array column.

**Data Annotations**

```csharp
public class Product
{
    public int ProductId { get; set; }

    [MaxLength(100)]
    public string Name { get; set; } = null!;
}
```

**Fluent API**

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Product>()
        .Property(p => p.Name)
        .HasMaxLength(100);
}
```

Both configurations set the maximum length of `Name` to 100 characters. The resulting database column type depends on the provider.

For more details, see our [MaxLength Attribute](/configuration/data-annotation-attributes/maxlength-attribute) guide.

## StringLength

The `[StringLength]` attribute also specifies a maximum string length. For database mapping, its Fluent API equivalent is `HasMaxLength()`.

**Data Annotations**

```csharp
public class Product
{
    public int ProductId { get; set; }

    [StringLength(100, MinimumLength = 3)]
    public string Name { get; set; } = null!;
}
```

**Fluent API**

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Product>()
        .Property(p => p.Name)
        .HasMaxLength(100);
}
```

Both configurations set the maximum column length to 100 characters. However, `[StringLength]` also supports `MinimumLength`, which validation-aware frameworks can use to enforce a minimum string length. This minimum does not affect the database schema, and `HasMaxLength()` does not provide equivalent validation.

For more details, see our [StringLength Attribute](/configuration/data-annotation-attributes/stringlength-attribute) guide.

## Unicode

By default, EF Core configures string properties as Unicode. Use `[Unicode(false)]` or `IsUnicode(false)` to configure a column as non-Unicode.

**Data Annotations**

```csharp
public class Product
{
    public int ProductId { get; set; }

    [Unicode(false)]
    [MaxLength(100)]
    public string Code { get; set; } = null!;
}
```

**Fluent API**

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Product>()
        .Property(p => p.Code)
        .IsUnicode(false)
        .HasMaxLength(100);
}
```

On SQL Server, both configurations map `Code` to `varchar(100)` instead of `nvarchar(100)`. The effect of Unicode configuration depends on the database provider.

## Collation

A collation determines how text is compared and sorted in a database. Use `UseCollation()` to configure a column's collation through Fluent API.

**Fluent API**

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Product>()
        .Property(p => p.Name)
        .UseCollation("SQL_Latin1_General_CP1_CI_AS");
}
```

This SQL Server collation makes comparisons on `Name` case-insensitive. For example, `"Laptop"` and `"laptop"` compare as equal under this collation.

A column-level collation overrides the database default for that column. Available collations depend on the database provider.

For more details, see Microsoft's [Collations and Case Sensitivity](https://learn.microsoft.com/en-us/ef/core/miscellaneous/collations-and-case-sensitivity) documentation.

## External Resources - Column in EF Core

The following videos demonstrate how EF Core maps entity properties to database columns and how to customize that mapping. They complement the examples in this article with practical demonstrations of Data Annotations, Fluent API, and column-level collations.

### Video 1 - Entity Framework Core Part 8 - Attribute-Based Configuration

<iframe width="560" height="315" src="https://www.youtube.com/embed/uQfeppE2SLE" title="Entity Framework Core Part 8 - Attribute-Based Configuration" frameborder="0" allowfullscreen></iframe>

In this video, Jasper Kent demonstrates how Data Annotations customize EF Core's default property mappings and shows the resulting database columns in SQL Server. The examples cover required properties, excluded properties, column names, and data types.

**Key timestamps:**

- [09:29](https://www.youtube.com/watch?v=uQfeppE2SLE&t=569s) — Uses `[Required]` to configure a non-nullable database column.
- [13:36](https://www.youtube.com/watch?v=uQfeppE2SLE&t=816s) — Demonstrates `[NotMapped]` to exclude a property from the database.
- [16:59](https://www.youtube.com/watch?v=uQfeppE2SLE&t=1019s) — Renames a database column using the `[Column]` attribute.
- [18:30](https://www.youtube.com/watch?v=uQfeppE2SLE&t=1110s) — Uses `TypeName` to specify a database column type.

The demonstration uses an older EF Core version, but its column-mapping examples remain useful. It also provides a visual reference for how configurations change the generated database schema.

### Video 2 - .NET Data Community Standup: All about EF Core property mapping

<iframe width="560" height="315" src="https://www.youtube.com/embed/ouGIWUtzxRQ" title=".NET Data Community Standup: All about EF Core property mapping" frameborder="0" allowfullscreen></iframe>

In this Microsoft community standup, Arthur Vickers and Jiri Cincura explain how EF Core decides which properties become database columns and how Fluent API can explicitly configure their mapping. The discussion also clarifies required properties, nullability, and shadow properties.

**Key timestamps:**

- [15:15](https://www.youtube.com/watch?v=ouGIWUtzxRQ&t=915s) — Explains the conventions EF Core uses to map properties to database columns.
- [18:32](https://www.youtube.com/watch?v=ouGIWUtzxRQ&t=1112s) — Demonstrates explicit property mapping through Fluent API.
- [44:46](https://www.youtube.com/watch?v=ouGIWUtzxRQ&t=2686s) — Explains how `[Required]` and `IsRequired()` affect database nullability.
- [57:23](https://www.youtube.com/watch?v=ouGIWUtzxRQ&t=3443s) — Introduces shadow properties, which can map to columns without corresponding C# properties.

The strongest contribution of this video is its explanation of the conventions behind column mapping. It also distinguishes EF Core's required configuration from the C# `required` keyword, which serves a different purpose.

### Video 3 - Entity Framework Community Standup - EF Core 5.0 Collations

<iframe width="560" height="315" src="https://www.youtube.com/embed/OgMhLVa_VfA" title="Entity Framework Community Standup - EF Core 5.0 Collations" frameborder="0" allowfullscreen></iframe>

This Microsoft community standup explores how collations control string comparisons and sorting across database providers. It demonstrates database-level and column-level configurations and explains their implications for query performance.

**Key timestamps:**

- [18:53](https://www.youtube.com/watch?v=OgMhLVa_VfA&t=1133s) — Introduces collations and case-sensitive versus case-insensitive comparisons.
- [28:54](https://www.youtube.com/watch?v=OgMhLVa_VfA&t=1734s) — Configures a case-sensitive SQL Server collation and discusses provider differences.
- [37:00](https://www.youtube.com/watch?v=OgMhLVa_VfA&t=2220s) — Demonstrates configuring a specific column's collation using Fluent API.
- [41:09](https://www.youtube.com/watch?v=OgMhLVa_VfA&t=2469s) — Explains how string comparisons can affect index usage and query performance.

Although recorded for EF Core 5, the column-level collation configuration remains relevant. The discussion provides additional context for choosing collations carefully, particularly when working with indexed text columns.

## Summary

EF Core lets you customize how entity properties map to database columns using Data Annotations or Fluent API. These configurations control column names, data types, ordering, nullability, length, and other database-specific settings.

Most column configurations support both approaches, while some, such as column-level collation, require Fluent API.

## Related Articles

- [Column Attribute](/configuration/data-annotation-attributes/column-attribute) — Configure column names, types, and order using Data Annotations.
- [Property Configuration](/configuration/fluent-api/property-configuration) — Explore Fluent API methods for configuring entity properties.
- [HasMaxLength Method](/configuration/fluent-api/hasmaxlength-method) — Configure column lengths using Fluent API.

## FAQ

### Should I use Data Annotations or Fluent API to configure columns in EF Core?

Both approaches can configure most column mappings. Data Annotations keep simple configurations directly on entity properties, while Fluent API keeps them in `OnModelCreating` or separate configuration classes and supports additional options. You can combine both approaches, but Fluent API takes precedence when they configure the same setting.

### Does changing a column configuration automatically update an existing database?

No. Changing a Data Annotation or Fluent API configuration updates the EF Core model, but does not automatically change an existing database schema. When using EF Core migrations, you must create and apply a migration to update the database.

### Does EF Core automatically validate column length and precision before saving?

No. Settings such as `HasMaxLength()` and `HasPrecision()` configure database mapping but do not automatically validate values before saving. Data Annotations such as `[StringLength]` can participate in application-level validation, but EF Core does not automatically perform that validation during `SaveChanges()`.

### Can using a different collation affect query performance?

Yes. Collations determine how text is compared and sorted. Using an explicit collation in a query that differs from the column's collation can prevent an existing index from being used efficiently. Configuring an appropriate column collation helps reduce the need for query-level overrides.