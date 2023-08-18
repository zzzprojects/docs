---
title: The Key Attribute
description: Usage of the Key Attribute in Entity Framework Core
canonical: /configuration/data-annotation-attributes/key-attribute
status: Published
lastmod: 2023-02-28
---

# EF Core Key Attribute

The `Key` attribute is used to denote the property that uniquely identifies an entity (the `EntityKey`), and which is mapped to the Primary Key field in a database:
```csharp
public class Order
{
    [Key]
    public int OrderNumber { get; set; }
    public DateTime DateCreated { get; set; }
    public Customer Customer { get; set; }
    ...
)
```
In the example above, the name of the key field in the `Order` class does not meet the [Entity Framework Core's naming convention for entity keys](/conventions#primary-key), so the `Key` attribute is required to configure the `OrderNumber` property as the key field. 

### Composite Keys
Entity Framework Core supports composite keys - primary key values generated from two or more fields in the database. However, unlike in previous versions of Entity Framework, you cannot use the `Key` attribute to define composite keys. This can only be done via the Fluent API.

#### Fluent API
The Fluent API equivalent to the `Key` attribute is the [HasKey](/configuration/fluent-api/haskey-method) method.
