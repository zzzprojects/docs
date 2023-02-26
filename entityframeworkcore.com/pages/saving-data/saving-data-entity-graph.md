---
permaid: 1000248
Title: EF Core Track Graph - Learn How set Entity in Added or Modified State
MetaDescription: Unlock the power of EF Core by using Track Graph to add, attach, or update tracked entities. Learn how to use the 'TrackGraph' method to specify if an entity should be tracked or not.
LastMod: 2023-02-24
tags: saveg graph
---

# EF Core Track Graph: Discover How set Entity in Added or Modified State

## Introduction

Entity Framework Core provides different methods, like `Add`, `Attach`, and `Update` for entity graph-traversal and determine whether an entity should be marked as Added (to insert), Modified (to update), Unchanged (do nothing), or Deleted (to delete).

 - These methods work well for individual entities or in cases where you don't mind all properties being included in an UPDATE statement whether they were changed or not.
 - In Entity Framework Core, the ChangeTracker.TrackGraph() method was also introduced to track the entire entity graph and set custom entity states to each entity in a graph.

#### TrackGraph

`TrackGraph` method is designed for use in disconnected scenarios where entities are retrieved using one instance of the context and then changes are saved using a different instance of the context.

 - It starts tracking an entity and any entities that are reachable by traversing it's navigation properties. 
 - Traversal is recursive, so the navigation properties of any discovered entities will also be scanned. 
 - The specified callback is called for each discovered entity and must set the State that each entity should be tracked in. If no state is set, the entity remains untracked.
 - If an entity is discovered that is already tracked by the context, that entity is not processed and it's navigation properties are not traversed.

Here is a simple object graph.


```csharp
var customer = new Customer()
{
    CustomerId = 1,
    FirstName = "Elizabeth",
    LastName = "Lincoln",
    Address = "23 Tsawassen Blvd.",
    Invoices = new List<Invoice>()
    {
        new Invoice()
        {
            Id = 1,
            Date = new DateTime(2018, 1,2)
        },
        new Invoice()
        {
            Id =2,
            Date = new DateTime(2018, 1,23)
        }
    }
};
```

In this object graph, we have two invoices of a customer, and the Date property for both invoices has been changed. To update the database for the above object graph, we can use `Update()` method, and it will do the job. 


```csharp
using(var context = new MyContext())
{
    context.Update(customer);
    context.SaveChanges();
}
```

 - The Update method will mark the root entity and all its related entities as Modified. 
 - SQL will be generated to update all of their properties (whether they have been changed or not) to the values that have been assigned to the entities. 
 - That means that all of the values for all of the entities have to be present; otherwise, they will be overwritten with null or default values.

The `TrackGraph` method provides easy access to each entity in the graph.


```csharp
using (var context = new MyContext())
{
    context.ChangeTracker.TrackGraph(customer, e =>
    {
        e.Entry.State = EntityState.Unchanged;

        if((e.Entry.Entity as Invoice) != null)
        {
            if (e.Entry.IsKeySet)
            {
                e.Entry.State = EntityState.Modified;
            }
            else
            {
                e.Entry.State = EntityState.Added;
            }
        }
    });

    foreach (var entry in context.ChangeTracker.Entries())
    {
        Console.WriteLine("Entity: {0}, State: {1}", entry.Entity.GetType().Name, entry.State.ToString());
    }
    context.SaveChanges();
}
```

The TrackGraph method is new in Entity Framework Core and offers a simple way to iterate over a graph of objects that you want the context to begin tracking, and to apply customized code based on the type of entity and other criteria.
