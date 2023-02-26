---
PermaID: 1000065
Title: Entity Framework AsNoTracking - Learn How Not Tracking Entities
MetaDescription: Unlock the power of Entity Framework by using AsNoTracking method when you don't need to track entities to improve performance. Learn about untracked entities and why you should do it.
LastMod: 2023-02-26
Tags: query as-no-tracking
---

# Entity Framework AsNoTracking: Discover How Not Tracking Entities

In the Entity Framework, the `DbContext` keeps the track of all the changes done in the objects, so that the correct update is done to the database when the `SaveChanges()` method of the context is called. 

 - When you execute the query, the Entity Framework puts these entities in a cache and tracks whatever changes are made on these entities until the `SaveChanges()` method is called.
 - But sometimes you do not need to track some entities, such as, retrieving the data for viewing purposes.

## What is AsNoTracking?

The `AsNoTracking()` is an extension method which returns a new query and the returned entities will not be cached by the context.

 - You can call this method as an instance method on any object of type `IQueryable`. 
 - After calling this method, Entity Framework will not perform any additional processing or storage of the entities that are returned by the query. 

## Example

```csharp
using (var context = new BookStore())
{
    var authors = context.Authors
        .AsNoTracking()
        .ToList();
}
```

[Try it](https://dotnetfiddle.net/zgSrZi)