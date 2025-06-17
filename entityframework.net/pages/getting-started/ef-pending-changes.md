---
PermaID: 1000131
Title: Entity Framework Pending Changes - Learn How to See Tracked Entries
MetaDescription: Unlock the power of Entity Framework by understanding how to look at your pending changes. Learn how to use the change tracker to retrieve DbEntityEntry from the Entries method.
LastMod: 2025-06-17
Tags: getting-started migration
---

# Entity Framework Pending Changes: Discover How to See Tracked Entries

## How can I see pending changes? 

I'm creating an application with the ADO.NET Entity Framework and can step through my code line-by-line while debugging and watch SQL Server Profiler for every query executed, but I can't figure out where all those SQL commands are coming from!

Sometimes when I execute SaveChanges(), the Entity Framework performs unexpected, weird **INSERTS**. How can I monitor the pending changes that queue up waiting for a SaveChanges() call?

### StackOverflow Related Questions

 - [How can I see the Entity Framework's pending changes?](https://stackoverflow.com/questions/592504/how-can-i-see-the-entity-frameworks-pending-changes)

## Answer

Entity Framework has a `ChangeTracker` property in DbContext class which provides access to features of the context that deal with change tracking of entities.


```csharp
using (var ctx = new EntityContext())
{
    //code here

    var addedEntries = ctx.ChangeTracker.Entries()
        .Where(e => e.State == EntityState.Added).ToList();

    var modifiedEntries = ctx.ChangeTracker.Entries()
        .Where(e => e.State == EntityState.Modified).ToList();

    var deletedEntries = ctx.ChangeTracker.Entries()
        .Where(e => e.State == EntityState.Deleted).ToList();
}
```
[Try it online](https://dotnetfiddle.net/i4RKYA)

The `Entries()` method returns DbEntityEntry objects for all the entities tracked by this context.