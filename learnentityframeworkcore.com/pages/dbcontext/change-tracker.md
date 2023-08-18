---
title: The Entity Framework Core ChangeTracker
description: The Entity Framework Core change tracker is responsible for tracking changes made to entities and setting the current state of the entity
canonical: /dbcontext/change-tracker
proficiencylevel: Intermediate
status: Published
lastmod: 2023-02-22
---

# EF Core ChangeTracker

When you call the `SaveChanges` method on the `DbContext`, the context needs to be able to generate the appropriate commands for each object that it is currently tracking. So it needs to know about the "state" of each object - whether it is new, or it is an existing object that has been modified in some way, or whether it is scheduled for deletion.  The Change Tracker is the mechanism responsible for this process.

The Change Tracker records the current state of an entity using one of four values:

- Added
- Unchanged
- Modified
- Deleted

Entities in the `Added` state will be inserted as new records into the database. Entities in the `Modified` state will have their values updated in the database to the current property values. Entities in the `Deleted` state will be removed from the database. No action will be taken in respect of entities marked as `Unchanged`.

Change tracking begins as soon as an entity is _loaded_. An entity is loaded as a result of being returned from a query or by being introduced to the context through one of the following methods of the `DbContext`

-  `Add`
-  `Attach`
-  `Update`
-  `Remove`

or by having its `State` property set on the entity entry returned by calling the context's `Entry` method.

## DbContext.Add
Tells EF that the entity is new and should be inserted into the database, and so sets the state to `Added`. It also does this for all reachable entities in the graph that it isn't already tracking.



```csharp
var db = new TestContext();
var book = new Book { 
    Title = "King Lear",
    Author = new Author { 
        FirstName = "William",
        LastName = "Shakespeare"
    }
}
db.Add(book); // Book Added, Author Added
```


```csharp
var db = new TestContext();
var book = new Book { Title = "Romeo and Juliet" };
book.Author = db.Authors.Single(a => a.AuthorId == 1);
db.Add(book); // Book Added, Author Unchanged
```

```csharp
var db = new TestContext();
var book = new Book { Title = "Romeo and Juliet" }; // Book Added, 
book.Author = db.Authors.Single(a => a.AuthorId == 1); // Author Unchanged
book.Author.FirstName = "Bill"; // Author Modified
db.Add(book);
```


```csharp
var db = new TestContext();
var book = new Book { Title = "Hamlet", AuthorId = 1 }; 
db.Add(book); // Book Added - only one entry being tracked
```

## DbContext.Attach
Tells EF that the entity already exists in the database, and sets the state of the entity to Unchanged.   
 ```csharp
var db = new TestContext();
var book = new Book { BookId = 1 }; 
db.Attach(book); // Book Unchanged
```

```csharp
var db = new TestContext();
var book = new Book { 
    BookId = 1, 
    Author = new Author {
        FirstName = "Charles", 
        LastName = "Dickens" 
    } 
};
db.Attach(book); // Book Modified, Author Added - no "store generated key"
``` 
## DbContext.Update 
Tells EF that the entity is modified. It will also set all reachable entities as modified if they are not already being tracked. 

```csharp
var db = new TestContext();
var book = new Book { BookId = 1 }; 
db.Update(book); // Book Modified
```
## DbContext.Remove
Marks only the root entity as deleted. Cascade deletion will take effect if implemented.  

```csharp
var db = new TestContext();
var book = new Book { BookId = 1 };
db.Remove(book); // Book Deleted
```

```csharp
var db = new TestContext();
var book = new Book {
    BookId = 1, 
    Author = db.Authors.SingleOrDefault(a => a.AuthorId == 1) 
};
db.Remove(book); // Book Deleted,  Author Unchanged
```
## DbContext.Entry.State
Only sets the state for that entry. Others in the graph are unaffected.

<!--
### TrackGraph


https://msdn.microsoft.com/en-us/magazine/mt694083.aspx

https://github.com/aspnet/EntityFramework/issues/4424

https://msdn.microsoft.com/magazine/mt767693

http://stevejgordon.co.uk/exploring-entity-framework-core-1-0-0-rtm-changes

https://github.com/aspnet/EntityFramework/blob/dev/src/Microsoft.EntityFrameworkCore/QueryTrackingBehavior.cs

https://github.com/aspnet/EntityFramework/blob/dev/src/Microsoft.EntityFrameworkCore/ChangeTracking/ChangeTracker.cs

#### Metadata
http://stackoverflow.com/questions/39862358/determine-if-property-is-a-navigation-property-in-ef-core

**performance
-->
