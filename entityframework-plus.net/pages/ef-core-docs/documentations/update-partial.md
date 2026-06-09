---
Title: EF Core UpdatePartial & UpdateRangePartial: Update Specific Properties
MetaDescription: Learn how to use UpdatePartial and UpdateRangePartial to update only specific properties in EF Core. Ideal for disconnected entities, DTO mapping, and partial updates.
LastMod: 2026-06-09
---

# EF Core UpdatePartial & UpdateRangePartial

In EF Core, when working with [disconnected entities](https://www.learnentityframeworkcore.com/saving/save-changes-disconnected-entities), you typically use the `Update` or `UpdateRange` methods to tell the change tracker that entities should be updated.

The problem is that EF Core doesn't know which properties were actually modified, so it marks all properties as modified and updates every column.

However, in many scenarios such as APIs, DTO mapping, and data imports, you already know which properties should be updated.

Instead of manually marking each property as modified, you can use our `UpdatePartial` and `UpdateRangePartial` methods:

```csharp
// @nuget: Z.EntityFramework.Plus.EFCore
using Z.EntityFramework.Plus;

context.UpdateRangePartial(customers, x => new { x.Code, x.Name });
context.SaveChanges();
```

[Online Example](https://dotnetfiddle.net/9kXGWh)

In a [disconnected entity](https://www.learnentityframeworkcore.com/saving/save-changes-disconnected-entities) scenario:

* Only the specified properties will be updated in the database

In a [connected entity](https://www.learnentityframeworkcore.com/saving/save-changes-connected-entities) scenario:

* The specified properties will be updated in the database
* Any other property already detected as modified by EF Core will also be included in the update

This allows you to perform partial updates with a single method call while keeping the same workflow as `Update` and `UpdateRange`.


## UpdatePartial and UpdateRangePartial Example

### UpdatePartial Example

The `UpdatePartial` method works similarly to EF Core's `Update` method, but only marks the specified properties as modified.

For example, if you know that only the `Name` and `Code` properties have changed, you can update only those columns in the database:

```csharp
// @nuget: Z.EntityFramework.Plus.EFCore
using Z.EntityFramework.Plus;

var customer = new Customer
{
    CustomerID = 1,
    Name = "John Smith",
    Code = "CUST-001"
};

context.UpdatePartial(customer, x => new { x.Name, x.Code });

context.SaveChanges();
```

[Online Example](https://dotnetfiddle.net/nsA7yV)

When `SaveChanges` is called, only the `Name` and `Code` columns are included in the SQL `UPDATE` statement.

This is especially useful in disconnected entity scenarios where you know exactly which properties should be updated and want to avoid updating every column in the table.

### UpdateRangePartial Example

The `UpdateRangePartial` method is the equivalent of `UpdatePartial` for multiple entities.

```csharp
// @nuget: Z.EntityFramework.Plus.EFCore
using Z.EntityFramework.Plus;

var customers = new List<Customer>
{
    new Customer { CustomerID = 1, Name = "John Smith", Code = "CUST-001" },
    new Customer { CustomerID = 2, Name = "Jane Smith", Code = "CUST-002" }
};

context.UpdateRangePartial(customers, x => new { x.Name, x.Code });

context.SaveChanges();
```

[Online Example](https://dotnetfiddle.net/9kXGWh)

In this example, only the `Name` and `Code` properties are marked as modified for every entity in the collection.

### UpdatePartial with Connected Entities

The `UpdatePartial` method can also be used with entities that are already tracked by the current `DbContext`.

In this scenario, the specified properties are marked as modified. In addition, any other property that EF Core has already detected as modified will also be included in the update.

```csharp
// Entity loaded and tracked by the DbContext
var customer = context.Customers.Single(x => x.CustomerID == 1);

// Modify some properties
customer.Name = "John Smith";
customer.Email = "john@company.com";

// Mark only Name as modified
context.UpdatePartial(customer, x => x.Name);

context.SaveChanges();
```

[Online Example](https://dotnetfiddle.net/juYXuS)

In this example:

* `Name` is included because it was specified in `UpdatePartial`
* `Email` is also included because EF Core detected that its value changed
* Both properties are updated when `SaveChanges` is called

This behavior allows `UpdatePartial` to work naturally with EF Core's change tracking system while still letting you explicitly include properties in the update.

## Summary

In this article, you learned how to use `UpdatePartial` and `UpdateRangePartial` to update only specific properties when working with Entity Framework Core.

These methods are especially useful in disconnected entity scenarios where you already know which properties should be updated and want to avoid marking every property as modified.

* Use `UpdatePartial` to update specific properties for a single entity.
* Use `UpdateRangePartial` to update specific properties for multiple entities.
* When used with connected entities, EF Core's normal change tracking behavior is preserved, and any additional modified properties detected by EF Core are also included in the update.

These methods provide a simple alternative to manually marking properties as modified while keeping the same workflow as EF Core's `Update` and `UpdateRange` methods.