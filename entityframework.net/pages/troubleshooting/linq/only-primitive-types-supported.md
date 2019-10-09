---
PermaID: 1000158
Name: Only primitive types supported
---

# Only Primitive Types Supported

## Exception: Unable to create a constant value of type [Object]. Only primitive types or enumeration types are supported in this context.

Using lambda expression, there are certain things that you can express in C#, but Entity Framework doesn't know how to convert it to SQL. For example, retrieve all the invoices of any particular customer.


```csharp
using (var context = new CustomerContext())
{
    var invoices = context.Invoices
        .Where(i => i.Customer == customer)
        .ToList();
}
```
[Try it online](https://dotnetfiddle.net/vsVdHW)

In this example, entity framework is trying to convert a whole customer object equality into a database query. 

### StackOverflow Related Questions

 - [Only primitive types or enumeration types are supported in this context](https://stackoverflow.com/questions/15211362/only-primitive-types-or-enumeration-types-are-supported-in-this-context)
 - [Error: Only primitive types or enumeration types are supported in this context EF](https://stackoverflow.com/questions/28406002/error-only-primitive-types-or-enumeration-types-are-supported-in-this-context-e)

## Solution

The easiest solution to handle this exception is to query data in entity framework queries using constant values, much like SQL queries by comparing the IDs of the object and not the object itself in Where method.

```csharp
using (var context = new CustomerContext())
{
    var invoices = context.Invoices
        .Where(i => i.Customer.CustomerID == customer.CustomerID)
        .ToList();
}
```
[Try it online](https://dotnetfiddle.net/V0qZIX)