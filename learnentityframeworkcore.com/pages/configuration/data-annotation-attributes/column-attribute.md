---
title: The Column Attribute
description: Use of the Column attribute in Entity Framework Core
canonical: /configuration/data-annotation-attributes/column-attribute
status: Published
lastmod: 2025-07-11
---
# EF Core Column Attribute

The `Column` attribute is applied to a property to specify the database column that the property should map to when the entity's property name and the database column name differ. The following example maps the `Title` property in the `Book` entity to a database column named `Description`:
```csharp
public class Book
{
    public int BookId { get; set; }
    [Column("Description")]
    public string Title { get; set; }
    public Author Author { get; set; }
}
```
You can also use the `Column` attribute to specify the ordinal position (`Order` property) of the column in the resulting table, and its database-specific data type (`TypeName` property):

```csharp
public class Book
{
    public int BookId { get; set; }
    [Column("Description", Order = 2, TypeName = "nvarchar(100)")]
    public string Title { get; set; }
    public Author Author { get; set; }
}
```
#### Fluent API

The Fluent API equivalent to the `Column` attribute is [HasColumnName](/configuration/fluent-api/hascolumnname-method). The Fluent API equivalent to specifying the data type is [HasColumnType](/configuration/fluent-api/hascolumntype-method). Column ordering is not supported in Fluent API.
