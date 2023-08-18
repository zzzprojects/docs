---
title: The MaxLength Attribute
description: Usage of the MaxLength Attribute in Entity Framework Core
canonical: /configuration/data-annotation-attributes/maxlength-attribute
status: Published
lastmod: 2023-02-28
---

# EF Core MaxLength Attribute

The `MaxLength` attribute is applied to a property to specify a maximum number of characters or bytes for the column that the property should map to. The following example specifies that the `Title` column in the Books table is to have a maximum length of 150 characters instead of the default which is unlimited:

```csharp
public class Book
{
    public int BookId { get; set; }
    [MaxLength(150)]
    public string Title { get; set; }
    public Author Author { get; set; }
}
```

In terms of database configuration, the `MaxLength` attribute works in the same way as the [StringLength attribute](/configuration/data-annotation-attributes/stringlength-attribute). In previous versions of Entity Framework, the `MaxLength` attribute also enforced entity validation and an exception was raised if the supplied value exceeded the maximum length specified. Entity validation is not included in Entity Framework Core 1.0.

It is believed that the `MaxLength` attribute was introduced primarily for syntactical reasons, emphasising the fact that it can be applied to byte arrays (varbinary in SQL Server) as well as strings.

#### Fluent API
The Fluent API equivalent for the `MaxLength` attribute is the [HasMaxLength method](/configuration/fluent-api/hasmaxlength-method).
