---
PermaID: 1000004
Title: Entity Framework Dynamic LINQ - Learn How to Execute Dynamically
MetaDescription: Unlock the power of Entity Framework by evaluating LINQ expression at runtime. Learn how to execute dynamic where clause and order clause from user input.
LastMod: 2023-02-22
Tags: third-party-library
---

# Entity Framework Dynamic LINQ: Discover How to Execute Dynamically

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

[Learn more](https://dynamic-linq.net/)