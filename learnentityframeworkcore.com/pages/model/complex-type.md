---
title: EF Core Complex Type
description: Entity Framework Core Complex Type - EF Core 8 New Feature
canonical: /model/complex-type
status: Published
lastmod: 2025-07-13
---

# EF Core Complex Type

EF Core 8 returned the missing [Complex Type](https://github.com/dotnet/efcore/issues/13947) from EF6. A complex type looks very similar to [Owned Entity](https://learn.microsoft.com/en-us/ef/core/modeling/owned-entities), allowing a developer to organize properties within an entity. One of the most straightforward examples is an `Order` with a `ShippingAddress` and a `BillingAddress`. Instead of repeating all address properties twice in the `Order`, we create a complex type `Address` and use it in the `Order`:

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

var shippingAddress = new Address() { Street = "Saint-Hubert", City = "Laval", State = "Quebec", Country = "Canada", PostalCode = "H7G 2Y2"};
var billingAddress = new Address() { Street = "Saint-Hubert", City = "Laval", State = "Quebec", Country = "Canada", PostalCode = "H7G 2Y2"};

var order = new Order() { OrderDate = DateTime.Now, ShippingAddress = shippingAddress, BillingAddress = billingAddress };
```

Unlike [Owned Entity](https://learn.microsoft.com/en-us/ef/core/modeling/owned-entities), the same complex type instance can be reused multiple times within the same or different entity. For example, if the customer chooses that the billing address will be the same as the shipping address in the payment form:

```csharp
var shippingAddress = new Address() { Street = "Saint-Hubert", City = "Laval", State = "Quebec", Country = "Canada", PostalCode = "H7G 2Y2"};

var order = new Order() { OrderDate = DateTime.Now, ShippingAddress = shippingAddress, BillingAddress = shippingAddress };
```

## How to Create a Complex Type

### Create a Complex Type as a Reference Type

You need to create a typical class to create a complex type as a reference type. In this example, we create an `Address` and use the [ComplexType Fluent API](/configuration/fluent-api/complexproperty-method) to configure it.

```csharp
public class Address
{
    public string Street { get; set; }
    public string City { get; set; }
    public string State { get; set; }
    public string Country { get; set; }
    public string PostalCode { get; set; }
    //... other properties
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

### Create a Complex Type as a Value Type

Unlike reference types, value types are immutable and represent data objects. So to create a `Complex Type` as a Value Type, we simply create a `struct` instead of a `class`:

```csharp
public struct Address
{
    public string Street { get; set; }
    public string City { get; set; }
    public string State { get; set; }
    public string Country { get; set; }
    public string PostalCode { get; set; }
    //... other properties

    public Address(string street, string city, string state, string country, string postalCode)
    {
        Street = street;
        City = city;
        State = state;
        Country = country;
        PostalCode = postalCode;
    }
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

## How to Configure a Complex Type

### Configure a Complex Type with Data Annotation

You can add the [ComplexType Data Annotation Attribute](/configuration/data-annotation-attributes/complextype-attribute) to specify a reference type or a value type should be treated as a complex type. You will also need to mark the complex property navigation as [Required](/configuration/data-annotation-attributes/required-attribute):

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

### Configure a Complex Type with Fluent API

You can also use the [ComplexProperty](/configuration/fluent-api/complexproperty-method) configuration with the [IsRequired method](/configuration/fluent-api/isrequired-method) in the `OnModelCreating` method to specify a reference type or a value type should be treated as a complex type.


```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
	modelBuilder.Entity<Order>(x => {
		   x.ComplexProperty(y => y.ShippingAddress, y => { y.IsRequired(); });
		   x.ComplexProperty(y => y.BillingAddress, y => { y.IsRequired(); });
	 });

	base.OnModelCreating(modelBuilder);
}
```

## How to Create a Nested Complex Type

A complex type can be nested with some other complex type. In this example, the address will also contain a `HomePhone` and a `WorkPhone`:

```csharp
public class Order
{
    public int OrderId { get; set; }
    public DateTime OrderDate { get; set; }
    public Address ShippingAddress { get; set; }
    public Address BillingAddress { get; set; }
    //... other order-related properties
}

public class Address
{
    public string Street { get; set; }
    public string City { get; set; }
    public string State { get; set; }
    public string Country { get; set; }
    public string PostalCode { get; set; }
    //... other properties
    
    public PhoneNumber HomePhone { get; set; }
    public PhoneNumber WorkPhone { get; set; }
}

public class PhoneNumber
{
    public string AreaCode { get; set; }
    public string Number { get; set; }
}

protected override void OnModelCreating(ModelBuilder modelBuilder)
{
	modelBuilder.Entity<Order>(x => {
			x.ComplexProperty(y => y.ShippingAddress, y => { 
				y.IsRequired();
				y.ComplexProperty(x => x.HomePhone, x => x.IsRequired());
				y.ComplexProperty(x => x.WorkPhone, x => x.IsRequired());				
			});
			x.ComplexProperty(y => y.BillingAddress, y => {
				y.IsRequired();
				y.ComplexProperty(x => x.HomePhone, x => x.IsRequired());
				y.ComplexProperty(x => x.WorkPhone, x => x.IsRequired());	
			});
	 });

	base.OnModelCreating(modelBuilder);
}
```

Multiple complex types can be nested as long as you specify all [ComplexProperty](/configuration/fluent-api/complexproperty-method) and mark them as [IsRequired](/configuration/fluent-api/isrequired-method) every time.

## How Complex Types are Queried?

When querying entities containing complex types, you will fetch normal properties of your entity but always all related complex properties. For example, if we select the first order:

```csharp
var order = context.Orders.First(x => x.OrderId == 1);
```

The SQL generated will retrieve all columns related to this order that are part of the order and its complex type:

```sql
SELECT TOP(1) [o].[OrderId], [o].[OrderDate], [o].[BillingAddress_City], [o].[BillingAddress_Country], [o].[BillingAddress_PostalCode], [o].[BillingAddress_State], [o].[BillingAddress_Street], [o].[ShippingAddress_City], [o].[ShippingAddress_Country], [o].[ShippingAddress_PostalCode], [o].[ShippingAddress_State], [o].[ShippingAddress_Street]
FROM [Orders] AS [o]
WHERE [o].[OrderId] = 1
```

## How Complex Types are Saved?

EF Core treats complex types as a part of the owning entity, so when you save the entity, you always save all related complex types. For example, if we want to save our order:

```csharp
var shippingAddress = new Address() { Street = "Saint-Hubert", City = "Laval", State = "Quebec", Country = "Canada", PostalCode = "H7G 2Y2" };
var billingAddress = new Address() { Street = "Saint-Hubert", City = "Laval", State = "Quebec", Country = "Canada", PostalCode = "H7G 2Y2" };

var order = new Order() { OrderDate = DateTime.Now, ShippingAddress = shippingAddress, BillingAddress = billingAddress };
context.Orders.Add(order);
context.SaveChanges();
```

The SQL generated will populate all columns from the `Orders` table using the `Order` entity and its complex type:

```sql
exec sp_executesql N'SET IMPLICIT_TRANSACTIONS OFF;
SET NOCOUNT ON;
INSERT INTO [Orders] ([OrderDate], [BillingAddress_City], [BillingAddress_Country], [BillingAddress_PostalCode], [BillingAddress_State], [BillingAddress_Street], [ShippingAddress_City], [ShippingAddress_Country], [ShippingAddress_PostalCode], [ShippingAddress_State], [ShippingAddress_Street])
OUTPUT INSERTED.[OrderId]
VALUES (@p0, @p1, @p2, @p3, @p4, @p5, @p6, @p7, @p8, @p9, @p10);
',N'@p0 datetime2(7),@p1 nvarchar(4000),@p2 nvarchar(4000),@p3 nvarchar(4000),@p4 nvarchar(4000),@p5 nvarchar(4000),@p6 nvarchar(4000),@p7 nvarchar(4000),@p8 nvarchar(4000),@p9 nvarchar(4000),@p10 nvarchar(4000)',@p0='2023-10-24 12:49:23.2972276',@p1=N'Laval',@p2=N'Canada',@p3=N'H7G 2Y2',@p4=N'Quebec',@p5=N'Saint-Hubert',@p6=N'Laval',@p7=N'Canada',@p8=N'H7G 2Y2',@p9=N'Quebec',@p10=N'Saint-Hubert'
```

## What is the Difference Between Complex Type and Owned Type?

The difference between a complex type and an [Owned Entity](https://learn.microsoft.com/en-us/ef/core/modeling/owned-entities) is very slight:

- A `Complex Type` instance can be reused within the same entity or other entity.
- A `Complex Type` has no identity / is not tracked.
- A `Owned Entity` can be saved in another table.
- A `Owned Entity` is considered as an `Entity Type`.

The significant advantage of a `Complex Type` is reusing the same instance multiple times, such as if you have a default address, phone number, etc.

## Conclusion

With EF Core 8 re-introducing complex types, they just filled a gap they had with EF6 by allowing them to share an instance of an object that is not tracked.

## Related Articles
 
- [ComplexType Data Annotation](/configuration/data-annotation-attributes/complextype-attribute)
- [Required Data Annotation](/configuration/data-annotation-attributes/required-attribute)
- [ComplexProperty Fluent API](/configuration/fluent-api/complexproperty-method)
- [IsRequired Fluent API](/configuration/fluent-api/isrequired-method)

