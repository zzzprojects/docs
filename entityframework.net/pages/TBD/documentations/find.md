---
PermaID: 1000081
Title: Entity Framework Find - Learn How to Find Entity By Key
MetaDescription: Unlock the power of Entity Framework by learning how to use the Find method to retrieve an entity from a database.
LastMod: 2023-02-19
Tags: query find
---

# Entity Framework Find: Discover How to Find Entity By Key

Finds an entity with the given primary key values. 

 - If an entity with the given primary key values exists in the context, then it is returned immediately without requesting the store. 
 - Otherwise, a request is made to the store for an entity with the given primary key values and this entity, if found, is attached to the context and returned. 
 - If no entity is found in the context or the store, then null is returned.

```csharp
using (var context = new BookStore())
{
    var authors = context.Authors
        .Find(2);
}
```

[Try it](https://dotnetfiddle.net/U3oGAV)