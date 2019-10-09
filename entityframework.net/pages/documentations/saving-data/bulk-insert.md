---
PermaID: 1000048
Name: Bulk Insert
---

# BulkInsert

When you want to insert hundreds, thousands, or millions of entities using `SaveChanges()` method, you will notice that your application performance is INSANELY slow. 

 - The `SaveChanges()` requires one database round-trip for every entity to insert. 
 - For example, if you need to insert 10000 entities, then 10000 database round-trips will be performed and your application suffers from performances issues.

## BulkInsert

Entity Framework Classic provides an extension method `BulkInsert()` which requires the minimum database round-trips as compared to `SaveChanges()`. 

### Performance Comparisons

|Operations	|1,000 Entities	    |2,000 Entities	    |5,000 Entities |
|:----------|:------------------|:------------------|:--------------|
|SaveChanges| 1,000 ms	        |2,000 ms	        |5,000 ms       |
|BulkInsert	|6 ms	            |10 ms	            |15 ms          |

### Example

The following example, add list of authors to the database using `BulkInsert` method.
 
```csharp
using (var context = new BookStore())
{
    List<Author> authors = GetAuthorsList();
    context.BulkInsert(authors);
}
```

[Try it](https://dotnetfiddle.net/eI2kag)

[Read more...](https://entityframework-classic.net/bulk-insert)