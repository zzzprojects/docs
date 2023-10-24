---
title: EF Core ComplexType Attribute
description: A Complex Type is a class that has no primary key defined. Complex Types has been added in EF Core 8
canonical: /configuration/data-annotation-attributes/complextype-attribute
status: Published
lastmod: 2023-10-24
---

# EF Core ComplexType Attribute

Starting from EF Core 8, it's now possible to set a class or a structure as a [Complex Type](/model/complex-type). The following example specifies that the `Address` class should be considered as a complex type:

```csharp
using System.ComponentModel.DataAnnotations;
using System.ComponentModel.DataAnnotations.Schema;

public class Order
{
    public int OrderId { get; set; }
    public DateTime OrderDate { get; set; }
    [Required]
    public Address ShippingAddress { get; set; }
    [Required]
    public Address BillingAddress { get; set; }
    //... other order-related properties
}

[ComplexType]
public class Address
{
    public string Street { get; set; }
    public string City { get; set; }
    public string State { get; set; }
    public string Country { get; set; }
    public string PostalCode { get; set; }
}
```

One particularity of the complex type is that you also have to mark complex property as [Required](/configuration/data-annotation-attributes/required-attribute)

#### Fluent API

The Fluent API equivalent to the `ComplexType` attribute is the [ComplexProperty method](/configuration/fluent-api/complexproperty-method).

## Related Articles

- [Model - Complex Type](/model/complex-type)
- [Data Annotation - Required Attribute](/configuration/data-annotation-attributes/required-attribute)
- [Fluent API - ComplexProperty Method](/configuration/fluent-api/complexproperty-method)