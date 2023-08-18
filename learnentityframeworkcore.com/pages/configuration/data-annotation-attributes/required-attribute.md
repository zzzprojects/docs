---
title: The Required Attribute
description: Usage of the Required Attribute in Entity Framework Core
canonical: /configuration/data-annotation-attributes/required-attribute
status: Published
lastmod: 2023-02-28
---

# EF Core Required Attribute

The `Required` attribute is applied to an entity property to configure the mapped database column as not nullable.  

By default, strings are mapped to nullable columns in the database, but this convention is overridden in the example below through the application of the `Required` attribute. The `Title` column in the `Book` table will be configured as `not null`:

```csharp
public class Book
{
    public int BookId { get; set; }
    [Required]
    public string Title { get; set; }
    public Author Author { get; set; }
}
```
The `Required` attribute can be used to provide client-side validation in validation-aware UI frameworks, such as ASP.NET MVC.

#### Fluent API
The Fluent API equivalent for the `Required` attribute is the [IsRequired method](/configuration/fluent-api/isrequired-method).
