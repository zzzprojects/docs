---
PermaID: 101002
Name: NullPropagation
---

# NullPropagation

One of the features that most developers like is the null conditional operator and is used almost everywhere. But when it is used in the LINQ query like as shown in the below code snippet.

```csharp
using (var context = new EntityContext())
{
    var customers = context.Customers
        .Include(c => c.Location)
        .Where(c => c.Location?.Name == "test")
        .ToList();
}
```

It throws the following exception.

`Error CS8072:	An expression tree lambda may not contain a null propagating operator.`

The solution for normal LINQ query is like:

```csharp
using (var context = new EntityContext())
{
    var customers = context.Customers
        .Include(c => c.Location)
        .Where(c => c.Location != null ? c.Location.Name == "test" : false)
        .ToList();
}
```

The System.Linq.Dynamic.Core library provides a function known as np() which can be used as an alternate.

```csharp
using (var context = new EntityContext()) 
{
    var customers = context.Customers
        .Include(c => c.Location)
        .Where("np(Location.Name) == \"test\"")
        .ToList();
}
```

In case you want to supply a default value, this looks like:

```csharp
using (var context = new EntityContext())
{
    var customers = context.Customers
        .Include(c => c.Location)
        .Where("np(Location.Name, \"unknown\") == \"test\"")
        .ToList();
}
```

At the moment it only works for properties.
