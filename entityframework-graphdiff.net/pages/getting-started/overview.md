---
Name: Getting Started
---

## Description

**GraphDiff** is a library that allows the automatic update of a detached graph using Entity Framework code first.

It provides DbContext extension methods that allow you to save an entire detached model/entity, with child entities and lists, to the database without writing the code to do it.

```csharp
using (var context = new TestDbContext())  
{
    // Update the company and state that the company 'owns' the collection Contacts.
    context.UpdateGraph(company, map => map
        .OwnedCollection(p => p.Contacts)
    );

    context.SaveChanges();
}
```

GraphDiff deals with two types of entity relations;

 - Owned Entity
 - Associated Entity

## Owned Entity 

 - An owned entity can be described as **being a part of**, when updating a graph then GraphDiff also changes owned entities with its owner.
 - The child entity is a part of the aggregate and will be updated, added or removed if changed in the parent's navigation property.

[Learn more](/attach-owned-entity.md)

## Associated Entity

- GraphDiff can also handle associated entities, when updating a graph then associated entities are not changed by GraphDiff.
 - The child entity is not a part of the aggregate. The parent's navigation property will be updated, but changes to the child will not be saved.

[Learn more](/attach-associated-entity.md)

## Features

 - Merge an entire graph of detached entities into the database using DbContext.UpdateGraph().
 - Ensures concurrency is maintained for all child entities in the graph.
 - Allows for different configuration mappings to ensure that only changes within the defined graph are persisted.
 - Comprehensive testing suite to cover many (un/)common scenarios.

## Contribute

The best way to contribute is by spreading the word about the library:

 - Blog it
 - Comment it
 - Fork it
 - Star it
 - Share it
 - A **HUGE THANKS** for your help.
