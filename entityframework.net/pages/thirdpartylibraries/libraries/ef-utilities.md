---
PermaID: 1000006
Name: EF Utilities
---

# Utilities

## Definition

Entity Framework is quite fast in many cases, but doing CRUD operations over many entities is slow. **EntityFramework.Utilities** provides some batch operations to solve this problem.

## Utility Methods

**EntityFramework.Utilities** provides some helper methods that make certain things easier. Most of them work against context so they should be provider independent.

### Update Single Value

A simpler API for working with disconnected entities and only updating single values. It is useful if you want to update a value on an entity without roundtripping the database. A typical use case could be to update the number of reads of a blogpost. 


```csharp
using (var db = new YourDbContext())
{
      db.AttachAndModify(new BlogPost { ID = postId }).Set(x => x.Reads, 10);
      db.SaveChanges();
}
```

### IncludeEFU

The standard EF Include is slow to use. The reason is that it cross joins the child records against the parent which means you load a significant amount of duplicate data. It means more data to transfer, more data to parse, more memory etc.

Include EFU, on the other hand, runs two parallel queries and stitch the data together in memory.


```csharp
//A very basic query:
var result = db.Contacts
.IncludeEFU(db, c => c.PhoneNumbers)
.ToList();

//It's also possible to sort and filter the child collections
var result = db.Contacts
    .IncludeEFU(db, x => x.PhoneNumbers
    .Where(n => n.Number == "10134")
    .OrderBy(p => p.ContactId)
    .ThenByDescending(p => p.Number))
    .ToList();
```

## Batch Operations

Batch Operations methods all work outside the normal EF pipeline and are located on the EFBatchOperation class. 

 - The design decision behind this choice is to make it clear you are NOT working against the context when using these methods. 
 - That means change tracking, and 2nd level cache or validation will NOT be run. 
 - It is for pure performance and nothing less. 
 - These methods are also highly provider dependent. 
 - Right now the only existing provider is for SQL Server, but it should be easy to add others.

### Batch Delete

Delete all Entities matching the predicate, instead of loading them into memory then delete them one by one. **EntityFramework.Utilities** helper method will create a SQL Query that removes all items in one single call to the database.


```csharp
using (var db = new YourDbContext())
{
    var count = EFBatchOperation.For(db, db.BlogPosts)
            .Where(b => b.Created < upper && b.Created > lower && b.Title == "T2.0")
            .Delete();
}
```

### Batch Insert

Insert many entities in a very efficient way instead of adding them one by one as you usually would do with EF.


```csharp
using (var db = new YourDbContext())
{
    EFBatchOperation.For(db, db.BlogPosts).InsertAll(list);
}
```

### Batch Update Entities

Batch Update works just like **Batch Insert** and you can choose exactly which columns to update too.

For example, load all items from the database and update them.


```csharp
var commentsFromDb = db.Comments.AsNoTracking().ToList();
var rand = new Random();
foreach (var item in commentsFromDb)
{
    item.Reads = rand.Next(0, 9999999);
}
EFBatchOperation.For(db, db.Comments).UpdateAll(commentsFromDb, x => x.ColumnsToUpdate(c => c.Reads));
```

## Requirements: Entity Framework Version

### EF 4-5

 - You need to manually select any of the O.1.x packages as the later packages are for EF V6.

### EF 6

 - Any package from 0.2.0 and < 1.0.0. Newer version use the newer metadata APIs and cannot be used

### EF 6.1.3+

 - Any package from 0.2.0 and up should work.

[Learn more](https://github.com/MikaelEliasson/EntityFramework.Utilities)