---
PermaID: 1000046
Name: Change Tracker
---

# Change Tracker

By default, Entity Framework tracks changes of the loaded entities during the life-time of the context. 

 - The Change Tracking tracks changes when you add new records, update or delete the existing records.
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

The following example shows the tracking of deleted author entity.

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

The following example shows the tracking of deleted author entity.

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