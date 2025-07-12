---
title: Modifying data via the DbContext
description: An examination of the methods and approaches available for modifying data via the Entity Framework Core DbContext API 
canonical: /dbcontext/modifying-data
status: Published
lastmod: 2025-07-11
---

# EF Core Update Entity

The approach that you adopt to update/modify entities depends on whether the context is currently tracking the entity being modified or not. 

In the following example, the entity is obtained by the context, so the context begins tracking it immediately. When you alter property values on a tracked entity, the context changes the `EntityState` for the entity to `Modified` and the ChangeTracker records the old property values and the new property values. When `SaveChanges` is called, an `UPDATE` statement is generated and executed by the database.

```csharp
var author = context.Authors.First(a => a.AuthorId == 1);
author.FirstName = "Bill";
context.SaveChanges();
```
Since the ChangeTracker tracks which properties have been modified, the context will issue a SQL statement that updates only those properties that were changed:

```sql
exec sp_executesql N'SET NOCOUNT ON;
UPDATE [Authors] SET [FirstName] = @p0
WHERE [AuthorId] = @p1;
SELECT @@ROWCOUNT;
',N'@p1 int,@p0 nvarchar(4000)',@p1=1,@p0=N'Bill'
```

## Disconnected Scenario

In a disconnected scenario such as an ASP.NET application, changes to an existing entity's property values can take place in a controller or service method, well away from the context. In these cases, the context needs to be informed that the entity is in a modified state. This can be achieved in several ways: setting the `EntityState` for the entity explicitly; using the `DbContext.Update` method (which is new in EF Core); using the `DbContext.Attach` method and then "walking the object graph" to set the state of individual properties within the graph explicitly.

## Setting EntityState

You can set the `EntityState` of an entity via the `EntityEntry.State` property, which is made available by the `DbContext.Entry` method.

```csharp
public void Save(Author author)
{
    context.Entry(author).State = EntityState.Modified;
    context.SaveChanges();
}
```

This approach will result in just the author entity being assigned the `Modified` state. Any related objects will not be tracked. Since the ChangeTracker is unaware of which properties were modified, the context will issue an SQL statement updating _all_ property values (apart from the primary key value).


## DbContext Update

The `DbContext` class provides `Update` and `UpdateRange` methods for working with individual or multiple entities. 

```csharp
public void Save(Author author)
{
    context.Update(author);
    context.SaveChanges();
}
```
As with setting the entity's `State`, this method results in the entity being tracked by the context as `Modified`. Once again, the context doesn't have any way of identifying which property values have been changed and will generate SQL to update all properties. Where this method differs from explicitly setting the `State` property, is in the fact that the context will begin tracking any related entities (such as a collection of books in this example) in the `Modified` state, resulting in `UPDATE` statements being generated for each of them. If the related entity doesn't have a key value assigned, it will be marked as `Added`, and an `INSERT` statement will be generated.

## Attach 

When you use the `Attach` method on an entity, its state will be set to `Unchanged`, which will result in no database commands being generated at all. All other reachable entities with key values defined will also be set to `Unchanged`. Those without key values will be marked as `Added`. However, now that the entity is being tracked by the context, you can inform the context which properties were modified so that the correct SQL to update just those values is generated:

```csharp
var context = new TestContext();
var author = new Author {
    AuthorId = 1,
    FirstName = "William",
    LastName = "Shakespeare"
};
author.Books.Add(new Book {BookId = 1, Title = "Othello" });

context.Attach(author);
context.Entry(author).Property("FirstName").IsModified = true;
context.SaveChanges();
```
The code above will result in the author entity being marked as `Modified`, and SQL being generated to update just the `FirstName` property:

```sql
exec sp_executesql N'SET NOCOUNT ON;
UPDATE [Authors] SET [FirstName] = @p0
WHERE [AuthorId] = @p1;
SELECT @@ROWCOUNT;
',N'@p1 int,@p0 nvarchar(4000)',@p1=1,@p0=N'William'
```
## TrackGraph
The `TrackGraph` API provides access to individual entities within an object graph and enables you to execute customized code against each one individually. This is useful in scenarios where you are dealing with complex object graphs that consist of various related entities in differing states.

The following example replicates a scenario where an object graph is constructed outside of the context. Then the TrackGraph method is used to "walk the graph":


```csharp 
var author = new Author {
    AuthorId = 1,
    FirstName = "William",
    LastName = "Shakespeare"
};
author.Books.Add(new Book { AuthorId = 1, BookId = 1, Title = "Hamlet", Isbn = "1234" });
author.Books.Add(new Book { AuthorId = 1, BookId = 2, Title = "Othello", Isbn = "4321" });
author.Books.Add(new Book { AuthorId = 1, BookId = 3, Title = "MacBeth", Isbn = "5678" });

var context = new TestContext();
context.ChangeTracker.TrackGraph(author, e => {
    if((e.Entry.Entity as Author) != null)
    {
        e.Entry.State = EntityState.Unchanged;
    }
    else
    {
        e.Entry.State = EntityState.Modified;
    }
});
context.SaveChanges();
```
In this scenario, it is assumed that the author entity has not been changed, but the books might have been edited. The `TrackGraph` method takes the root entity as an argument, and a lambda specifying the action to perform. In this case, the root entity, the author has its `EntityState` set to `Unchanged`. Setting the `EntityState` is required for the context to begin tracking the entity. Only then can related entities be discovered.  Books have their `EntityState` set to `Modified`, which as in the previous examples, will result in SQL that updates every property on the entity:
```sql
exec sp_executesql N'SET NOCOUNT ON;
UPDATE [Books] SET [AuthorId] = @p0, [Isbn] = @p1, [Title] = @p2
WHERE [BookId] = @p3;
SELECT @@ROWCOUNT;
UPDATE [Books] SET [AuthorId] = @p4, [Isbn] = @p5, [Title] = @p6
WHERE [BookId] = @p7;
SELECT @@ROWCOUNT;
UPDATE [Books] SET [AuthorId] = @p8, [Isbn] = @p9, [Title] = @p10
WHERE [BookId] = @p11;
SELECT @@ROWCOUNT;
',N'@p3 int,@p0 int,@p1 nvarchar(4000),@p2 nvarchar(150),@p7 int,@p4 int,@p5 nvarchar(4000),
@p6 nvarchar(150),@p11 int,@p8 int,@p9 nvarchar(4000),@p10 nvarchar(150)',
@p3=1,@p0=1,@p1=N'1234',@p2=N'Hamlet',
@p7=2,@p4=1,@p5=N'4321',@p6=N'Othello',
@p11=3,@p8=1,@p9=N'5678',@p10=N'MacBeth'
```
Since the SQL updates all properties, they all need to be present and have a valid value assigned, otherwise, they will be updated to their default values.

In the next example, the object graph is once again constructed outside of the context, but only the `Isbn` property of the books is modified. Therefore other properties (apart from the entity key) are omitted:

```csharp
var author = new Author {
    AuthorId = 1,
    FirstName = "William",
    LastName = "Shakespeare"
};
author.Books.Add(new Book { BookId = 1, Isbn = "1234" });
author.Books.Add(new Book { BookId = 2, Isbn = "4321" });
author.Books.Add(new Book { BookId = 3, Isbn = "5678" });

var context = new TestContext();
context.ChangeTracker.TrackGraph(author, e => {
    e.Entry.State = EntityState.Unchanged; //starts tracking
    if((e.Entry.Entity as Book) != null)
    {
        context.Entry(e.Entry.Entity as Book).Property("Isbn").IsModified = true;
    }
});
```
This time, the method body of the lambda ensures that all entities are tracked in the `Unchanged` state, and then indicates that the `Isbn` property is modified. This results in SQL being generated that only updates the `Isbn` property value:

```sql
exec sp_executesql N'SET NOCOUNT ON;
UPDATE [Books] SET [Isbn] = @p0
WHERE [BookId] = @p1;
SELECT @@ROWCOUNT;
UPDATE [Books] SET [Isbn] = @p2
WHERE [BookId] = @p3;
SELECT @@ROWCOUNT;
UPDATE [Books] SET [Isbn] = @p4
WHERE [BookId] = @p5;
SELECT @@ROWCOUNT;
',N'@p1 int,@p0 nvarchar(4000),@p3 int,@p2 nvarchar(4000),@p5 int,@p4 nvarchar(4000)',
@p1=1,@p0=N'1234',
@p3=2,@p2=N'4321',
@p5=3,@p4=N'5678'
```

#### Further Reading

- [Entity Framework Core TrackGraph For Disconnected Data](http://www.mikesdotnetting.com/article/303/entity-framework-core-trackgraph-for-disconnected-data)
