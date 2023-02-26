---
permaid: 1000245
Title: EF Core Change Tracker - Learn How it Works and Possible States
MetaDescription: Unlock the power of EF Core by using Change Tracker, understanding how it works, and the possible state of an entity entry. Learn about the possible state such as 'Added', 'Unchanged', 'Modified', 'Deleted', 'Detached' and their behaviors.
LastMod: 2023-02-24
tags: save change-tracker
---

# EF Core Change Tracker: Discover How it Works and Possible States

## Introduction

`ChangeTracker` class provides access to change tracking information and operations for entity instances the context is tracking. In EF Core, the DbContext includes the ChangeTracker class, and it starts tracking of all the entities as soon as it is retrieved using DbContext.

The Change Tracker tracks an entity using any of the following states: 

 - Added
 - Unchanged
 - Modified
 - Deleted
 - Detached

### Added

The entity is being tracked by the context but does not yet exist in the database. If a new entity or entities are added into the context using `Add()` method, then it will be marked as Added.


```csharp
using (var context = new MyContext())
{
    var customer = new Customer
    {
        FirstName = "Thomas",
        LastName = "Hardy",
        Address = "120 Hanover Sq."
    };

    context.Customers.Add(customer);
    foreach (var entry in context.ChangeTracker.Entries())
    {
        Console.WriteLine("Entity: {0},  State: {1} ", entry.Entity.GetType().Name, entry.State.ToString());
    }
}
```

### Deleted
    
The entity is being tracked by the context and exists in the database. If any entity is removed from the DbContext using the `Remove()` method, then it will be marked as Deleted.


```csharp
using (var context = new MyContext())
{
    var customer = context.Customers
        .Where(c => c.FirstName == "Thomas")
        .FirstOrDefault();

    context.Customers.Remove(customer);
    foreach (var entry in context.ChangeTracker.Entries())
    {
        Console.WriteLine("Entity: {0},  State: {1} ", entry.Entity.GetType().Name, entry.State.ToString());
    }
}
```

### Detached

If the entity or entities created or retrieved out of the scope of the current DbContext instance, then it will be marked as Detached.


```csharp
var customer = new Customer
{
    FirstName = "Elizabeth",
    LastName = "Lincoln",
    Address = "23 Tsawassen Blvd."
};

using (var context = new MyContext())
{
    Console.Write(context.Entry(customer).State);
}
```

The entities are not being tracked by an existing DbContext instance are also called disconnected entities.

### Modified
    
The entity is being tracked by the context and exists in the database. The entity will be marked as Modified if the value of any property of an entity is changed in the scope of the DbContext.


```csharp
using (var context = new MyContext())
{
    var customer = context.Customers
        .Where(c => c.FirstName == "Thomas")
        .FirstOrDefault();
    
    customer.LastName = "Anders";
    Console.Write(context.Entry(customer).State);
}
```

### Unchanged
    
The entity is being tracked by the context and exists in the database. When the entity or entities are retrieved using raw SQL query or LINQ-to-Entities queries will have the Unchanged state.


```csharp
using (var context = new MyContext())
{
    var customer = context.Customers
        .Where(c => c.FirstName == "Mark")
        .FirstOrDefault();

    Console.Write(context.Entry(customer).State);
}
```
