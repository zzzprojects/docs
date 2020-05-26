---
Permalink: advanced-null-propagation
Name: Null Propagation
---

# Null Propagation

One of the features that most developers like is the null conditional operator and is used almost everywhere. But when it is used in the LINQ query (which uses EntityFramework to access a database) like as shown in the below code snippet.

## Using Null Propagation in strongly typed LINQ-SQL

```csharp
using (var context = new EntityContext())
{
    var customers = context.Customers
        .Include(c => c.Location)
        .Where(c => c.Location?.Name == "test")
        .ToList();
}
```

It throws the following exception:

`Error CS8072: An expression tree lambda may not contain a null propagating operator.`

Here is the solution for normal strongly typed LINQ query:

```csharp
using (var context = new EntityContext())
{
    var customers = context.Customers
        .Include(c => c.Location)
        .Where(c => c.Location != null ? c.Location.Name == "test" : false)
        .ToList();
}
```

[Try it online](https://dotnetfiddle.net/qmi6D8)

## Using Null Propagation in Dynamic LINQ

### Normal usage

The `System.Linq.Dynamic.Core` library provides a function known as `np()` which can be used as a short-hand alternative.

In the example below, we specified the `np()` method to do a where-clause on the customers and return only the customers who have the Location defined, and the Name from that Location should be equal to "test".

```csharp
using (var context = new EntityContext())
{
    var customers = context.Customers
        .Include(c => c.Location)
        .Where("np(Location.Name) == \"test\"")
        .ToList();
}
```

[Try it online](https://dotnetfiddle.net/AlCj05)

### Supply default value

If you want to supply a default value in case the value is null, you can provide the default value as second parameter to the `np()`-method:

It's also possible to use the `np()`-method in all expressions. As an example, you can also use it in a `Select`:

```csharp
using (var context = new EntityContext())
{
    var customers = context.Customers
        .Include(c => c.Location)
        .Select("np(Location.Name, \"unknown\")")
        .ToList();
}
```

[Try it online](https://dotnetfiddle.net/adddbt)

This Null Propagating functionality works for fields, properties and methods.
