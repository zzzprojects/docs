---
PermaID: 1000004
Name: LINQ Dynamic
---

# System.Linq.Dynamic

## Definition

**System.Linq.Dynamic** allows you to express LINQ queries using extension methods that take string arguments instead of type-safe language operators.

## Dynamic Expression API

The Dynamic Expression API is brought into scope by using (importing) the System.Linq.Dynamic namespace. Below is an example of applying the Dynamic Expression API to a LINQ to SQL data source.


```csharp
var query =
    db.Customers.
    Where("City = @0 and Orders.Count >= @1", "London", 10).
    OrderBy("CompanyName").
    Select("new(CompanyName as Name, Phone)");
```

Note that expressions in the query are strings that could have been dynamically constructed at run-time.


## Requirements

### Entity Framework Version

 - All versions are supported.

[Learn more](http://blog.vavstech.com/2014/08/systemlinqdynamic-documentation.html)