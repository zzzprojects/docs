---
PermaID: 1000127
Title: Entity Framework ComplexType Data Annotations
MetaDescription: Unlock the power of Entity Framework by using the ComplexType Data Annotations. Learn what a complex type is and how to specify this attribute.
LastMod: 2025-06-17
Tags: code-first fluent-api data-annotations
---

# Entity Framework ComplexType Data Annotations

The Complex types are non-scalar properties of entity types that enable scalar properties to be organized within entities. 

 - `ComplexType` doesn't have keys and therefore cannot exist independently. 
 - It can only exist as properties of entity types or other complex types.
 - It cannot participate in associations and cannot contain navigation properties.
 - Complex type properties cannot be null. 
 - Scalar properties of complex objects can be null.

In the following example, we have added an `Address` class and decorated it with `ComplexType` attribute.

```csharp
public class Author
{
    public int AuthorId { get; set; }
    public string Name { get; set; }
    public Address Address { get; set; }
}

[ComplexType]
public class Address
{
    public string City { get; set; }
    public string Street { get; set; }
    public string StateOrProvince { get; set; }
    public string Country { get; set; }
}
```

You can see that `Address` class does not have any type of key property. Complex types cannot be tracked on their own, so as a property in the `Author` class, it will be tracked as part of an author object.

[Try it](https://dotnetfiddle.net/aFAD8Z)