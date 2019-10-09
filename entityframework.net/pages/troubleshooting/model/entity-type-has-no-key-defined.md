---
PermaID: 1000155
Name: Entity Type has not key defined
---

# EntityType Has No Key Defined

## Exception: One or more validation errors were detected during model generation. EntityType has no key defined. Define the key for this EntityType.

### StackOverflow Related Questions

 - [EntityType has no key defined error](https://stackoverflow.com/questions/20203492/entitytype-has-no-key-defined-error)
 - [mvc entity type has no key defined](https://stackoverflow.com/questions/39148360/mvc-entity-type-has-no-key-defined)
 - [Entity type has no key defined EF6](https://stackoverflow.com/questions/27205961/entity-type-has-no-key-defined-ef6)

## Answer

There are several reasons this can happen and you can fix it very easily when this message occurs.

### Scenario 1:

You've forgotten to include a key. 


```csharp
public class Customer
{
    public string Name { get; set; }
    public List<Invoice> Invoices { get; set; }

}
```
[Try it online](https://dotnetfiddle.net/CW86Cn)

All EF models need a key, so make sure that your model class contains key.


```csharp
public class Customer
{
    public int CustomerID { get; set; }
    public string Name { get; set; }
    public List<Invoice> Invoices { get; set; }
}
```
[Try it online](https://dotnetfiddle.net/b0Yguz)

### Scenario 2:

Primary key member does not have getter/setter. 


```csharp
public class Customer
{
    public int CustomerID;
    // other properties
}
```
[Try it online](https://dotnetfiddle.net/wEBsf6)

It needs to be a property but not a field.

```csharp
public class Customer
{
    public int CustomerID { get; set; }
    // other properties
}
```
[Try it online](https://dotnetfiddle.net/b0Yguz)

### Scenario 3:

The Id member is not public.

```csharp
public class Customer
{
    protected int CustomerID { get; set; }
    // other properties
}
```
[Try it online](https://dotnetfiddle.net/PSAZmn)

All your Id members must be public.


```csharp
public class Customer
{
    public int CustomerID { get; set; }
    // other properties
}
```
[Try it online](https://dotnetfiddle.net/b0Yguz)

### Scenario 4:

Class name is not equal to Id member name.

```csharp
public class Customer
{
    public int CustId { get; set; }
    public string Name { get; set; }
    public List<Invoice> Invoices { get; set; }
}
```
[Try it online](https://dotnetfiddle.net/ttSF5r)

Code First infers that a property is a primary key if a property on a class is named `Id` (not case sensitive), or the class name followed by `Id`. If the type of the primary key property is numeric or GUID it will be configured as an identity column.

Make sure that primary key property is either named `Id` or `CustomerId` in your models.

```csharp
public class Customer
{
    public int CustomerID { get; set; }
    public string Name { get; set; }
    public List<Invoice> Invoices { get; set; }
}
```
[Try it online](https://dotnetfiddle.net/b0Yguz)

Or add `[Key]` to give EF a hint that it is the identity property and don't forget to add `using System.ComponentModel.DataAnnotations;`

```csharp
public class Customer
{
    [Key]
    public int CustId { get; set; }
    public string Name { get; set; }
    public List<Invoice> Invoices { get; set; }
}
```
[Try it online](https://dotnetfiddle.net/b2vMKh)