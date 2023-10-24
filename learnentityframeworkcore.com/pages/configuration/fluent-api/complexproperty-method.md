---
title: EF Core ComplexProperty Method
description: A Complex Type is a class that has no primary key defined. Complex Types are not currently implemented in Entity Framework Core.
canonical: /configuration/fluent-api/complexproperty-method
status: Published
lastmod: 2023-10-24
---

# EF Core Complex Property

As of EF Core 8, it's now possible to specify a class or a structure as a [Complex Type](/model/complex-type). The following example specifies that the `Address` structure should be considered as a complex type:

```csharp
public class Order
{
    public int OrderId { get; set; }
    public DateTime OrderDate { get; set; }
    public Address ShippingAddress { get; set; }
    public Address BillingAddress { get; set; }
    //... other order-related properties
}

public struct Address
{
    public string Street { get; set; }
    public string City { get; set; }
    public string State { get; set; }
    public string Country { get; set; }
    public string PostalCode { get; set; }
    //... other address-related properties
}

protected override void OnModelCreating(ModelBuilder modelBuilder)
{
	modelBuilder.Entity<Order>(x => {
		   x.ComplexProperty(y => y.ShippingAddress, y => { y.IsRequired(); });
		   x.ComplexProperty(y => y.BillingAddress, y => { y.IsRequired(); });
	 });

	base.OnModelCreating(modelBuilder);
}
```

**NOTE**: When specifying a `ComplexProperty`, you must also specify the navigation [IsRequired](/configuration/fluent-api/isrequired-method) in your fluent mapping.

### Data Annotations
The Data Annotations equivalent for the `ComplexProperty` method is the [ComplexType attribute](/configuration/data-annotation-attributes/complextype-attribute).

## Related Articles

- [Model - Complex Type](/model/complex-type)
- [Fluent API - IsRequired Method](/configuration/fluent-api/isrequired-method)
- [Data Annotation - ComplexType Attribute](/configuration/data-annotation-attributes/complextype-attribute)