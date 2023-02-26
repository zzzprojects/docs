---
permaid: 1000231
Title: EF Core AsNoTracking - Learn what you should not track all queries
MetaDescription: Unlock the power of EF Core by using AsNoTracking to improve performance when returning entities. Learn why not tracking entities that don't need to be tracked could improve your querying performance.
LastMod: 2023-02-23
tags: query change-tracker disconnected
---

# EF Core AsNoTracking: Discover what you should not track all queries

## Introduction

Tracking behavior controls whether to keep track or not of information about an entity instance when it is changed. If an entity is tracked, any changes detected in the entity will be persisted to the database during SaveChanges(). 
 
In the following example, the change to the customer address will be detected and persisted to the database during `SaveChanges()`.


```csharp
using (var context = new MyContext())
{
    var customer = context.Customers
        .Where(c => c.CustomerId == 2)
        .FirstOrDefault();

     customer.Address = "43 rue St. Laurent";
     context.SaveChanges();
}
```

## No-tracking

No tracking query executes quickly because there is no need to setup change tracking information. It is useful when the results are used in a read-only scenario.

You can convert a query to a no-tracking query by using `AsNoTracking()` method.

##### AsNoTracking

The `AsNoTracking()` method returns a new query where the change tracker will not track any of the entities that are returned. If the entity instances are modified, this will not be detected by the change tracker, and SaveChanges() will not persist those changes to the database.


```csharp
using (var context = new MyContext())
{
     var customers = context.Customers
         .AsNoTracking().ToList();
}
```

You can also change the default tracking behavior at the context instance level.


```csharp
using (var context = new MyContext())
{
    context.ChangeTracker.QueryTrackingBehavior = QueryTrackingBehavior.NoTracking;
    var customers = context.Customers.ToList();
}
```
