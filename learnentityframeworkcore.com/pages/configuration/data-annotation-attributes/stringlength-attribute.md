---
title: The StringLength Attribute
description: Usage of the StringLength Attribute in Entity Framework Core
canonical: /configuration/data-annotation-attributes/stringlength-attribute
status: Published
lastmod: 2025-07-11
---

# EF Core StringLength Attribute

The `StringLength` attribute is applied to a property to specify a maximum number of characters or bytes for the column that the property should map to. The following example specifies that the `Title` column in the Books table is to have a maximum length of 150 characters instead of the default which is unlimited:

```csharp
public class Book
{
    public int BookId { get; set; }
    [StringLength(150)]
    public string Title { get; set; }
    public Author Author { get; set; }
}
```
The `StringLength` attribute also allows you to specify a minimum acceptable length for a string:
```csharp
public class Book
{
    public int BookId { get; set; }
    [StringLength(150, MinimumLength = 5)]
    public string Title { get; set; }
    public Author Author { get; set; }
}
```
Setting the `MinimumLength` property value has no effect on database configuration, but can be used to provide client-side validation in validation-aware UI frameworks, such as ASP.NET MVC.

In terms of database configuration, the `StringLength` attribute works in the same way as the [MaxLength attribute](/configuration/data-annotation-attributes/maxlength-attribute). In previous versions of Entity Framework, the `MaxLength` attribute also enforced entity validation and an exception was raised if the supplied value exceeded the maximum length specified. Entity validation is not included in Entity Framework Core 1.0.

#### Fluent API
The Fluent API equivalent for the `StringLength` attribute is the [HasMaxLength method](/configuration/fluent-api/hasmaxlength-method).
