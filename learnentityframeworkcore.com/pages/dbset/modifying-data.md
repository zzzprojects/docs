---
title: Modifying data via the DbSet
description: An examination of the methods and approaches available for modifying data via the Entity Framework Core DbSet API 
canonical: /dbset/modifying-data
status: Published
lastmod: 2023-11-22
---

# EF Core Modify Record

The approach that you adopt to modify entities via the `DbSet` depends on whether the context is currently tracking the entity being modified or not. 

In the following example, the entity is obtained by the context, so the context begins tracking it immediately. When you alter property values on a tracked entity, the context changes the `EntityState` for the entity to `Modified` and the ChangeTracker records the old property values and the new property values. When `SaveChanges` is called, an `UPDATE` statement is generated and executed by the database.

```csharp
var author = context.Authors.Find(1);
author.FirstName = "Bill";
context.SaveChanges();
```

Since the ChangeTracker tracks which properties have been modified, the context  will issue a SQL statement that updates only those properties that were changed:

```sql
exec sp_executesql N'SET NOCOUNT ON;
UPDATE [Authors] SET [FirstName] = @p0
WHERE [AuthorId] = @p1;
SELECT @@ROWCOUNT;
',N'@p1 int,@p0 nvarchar(4000)',@p1=1,@p0=N'Bill'
```

## Disconnected Scenario

In a disconnected scenario such as an ASP.NET application, changes to an existing entity's property values can take place in a controller or service method, well away from the context. In these cases, the context needs to be informed that the entity is in a modified state. This can be achieved by using the `DbSet<T>.Update` method (which is new in EF Core).

## DbSet Update

The `DbSet<T>` class provides `Update` and `UpdateRange` methods for working with an individual or multiple entities. 

```csharp
public void Save(Author author)
{
    context.Authors.Update(author);
    context.SaveChanges();
}
```
This method results in the entity being tracked by the context as `Modified`. The context doesn't have any way of identifying which property values have been changed and will generate SQL to update all properties. Any related entities (such as a collection of books in this example) will also be tracked in the `Modified` state, resulting in `UPDATE` statements being generated for each of them. If the related entity doesn't have a key value assigned, it will be marked as `Added`, and an `INSERT` statement will be generated instead.

#### Further Reading

- [Entity Framework Core TrackGraph For Disconnected Data](http://www.mikesdotnetting.com/article/303/entity-framework-core-trackgraph-for-disconnected-data)
- [EF Core - Bulk Update](https://entityframework-extensions.net/bulk-update)