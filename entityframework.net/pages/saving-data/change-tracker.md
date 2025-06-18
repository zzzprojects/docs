---
PermaID: 1000046
Title: Entity Framework Change Tracker - Learn How to Use all Entity State
MetaDescription: Unlock the power of Entity Framework by using Change Tracker, understanding how it works, and the possible state of an entity entry. Learn about the possible state such as 'Added', 'Unchanged', 'Modified', 'Deleted', 'Detached' and their behaviors.
LastMod: 2025-06-18
Tags: saving change-tracking
---

# Entity Framework Change Tracker: Discover How to Use all Entity State

By default, Entity Framework tracks changes of the loaded entities during the lifetime of the context. 

 - The Change Tracker tracks changes when you add new records, update or delete the existing records.
 - These track changes are lost if they are not saved before the DbContext object is destroyed.
 - The entity must have a primary key property to be tracked by the context. 

The `DbChangeTracker` class gives you all the information about current entities being tracked by the context, and all the changes are kept by the DbContext level. 

```csharp
private static void DisplayTrackedEntities(DbChangeTracker changeTracker)
{
    var entries = changeTracker.Entries();
    foreach (var entry in entries)
    {
        Console.WriteLine("Entity Name: {0}", entry.Entity.GetType().FullName);
        Console.WriteLine("Status: {0}", entry.State);
    }
}
```

The Change Tracker tracks an entity using any of the following states:

 - Added
 - Modified
 - Deleted
 - Unchanged
 - Detached

## Added State

The entity is being tracked by the context but does not yet exist in the database. If a new entity or entities are added into the context using Add() method, then it will be marked as Added.

The following example shows the tracking of newly added author entity.

```csharp
using (var context = new BookStore())
{
    Console.WriteLine("Adding Author");
    Author author = new Author() { Name = "Mark" };
     
    context.Authors.Add(author);
    Console.WriteLine("Context tracking changes of {0} entities.", context.ChangeTracker.Entries().Count());
    DisplayTrackedEntities(context.ChangeTracker);
}
```

[Try it](https://dotnetfiddle.net/C1HeLd)

## Modified State

The following example shows the tracking of an updated author entity.

```csharp
using (var context = new BookStore())
{
    Console.WriteLine("Update Author");
    Author author = context.Authors
        .FirstOrDefault();
     
    author.Name = "Russell";
    
    Console.WriteLine("Context tracking changes of {0} entities.", context.ChangeTracker.Entries().Count());
    DisplayTrackedEntities(context.ChangeTracker);

}
```

[Try it](https://dotnetfiddle.net/FhwwVr)

## Deleted State

The following example shows the tracking of an author entity that is removed.

```csharp
using (var context = new BookStore())
{
    Console.WriteLine("Delete Author");
    Author author = context.Authors
        .FirstOrDefault();
     
    context.Authors.Remove(author);
    
    Console.WriteLine("Context tracking changes of {0} entities.", context.ChangeTracker.Entries().Count());
    DisplayTrackedEntities(context.ChangeTracker);

}
```
[Try it](https://dotnetfiddle.net/FwuStm)

## Unchanged State

The following example shows the tracking of Unchanged author entity.

```csharp
using (var context = new BookStore())
{
    Author author = context.Authors
        .FirstOrDefault();
    
    Console.WriteLine("Context tracking changes of {0} entities.", context.ChangeTracker.Entries().Count());
    DisplayTrackedEntities(context.ChangeTracker);

}
```
[Try it](https://dotnetfiddle.net/FSYP09)

## Detached State

The following example shows the tracking of a detached author entity.

```csharp
Author author;
        
using(var context = new BookStore())
{
    author = context.Authors
        .FirstOrDefault();
}

using (var context = new BookStore())
{                    
    Console.Write(context.Entry(author).State);
}
```
[Try it](https://dotnetfiddle.net/MYDepn)