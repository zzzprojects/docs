---
title: Managing One-To-Many Relationships With Entity Framework Core
description: Entity Framework Core offers many approaches to the creation and modification of one-to-many relationships.
canonical: /relationships/managing-one-to-many-relationships
status: Published
lastmod: 2025-07-13
---

# EF Core: How to manage One-To-Many Relationships

Entity Framework Core offers several approaches to the creation and modification of one-to-many relationships. 

## Creating relationships
This first section explores many ways in which relationships can be created between an existing principal entity and newly created dependants.

### Add the dependant to the principal's collection property
In this example, an existing author is referenced using the `DbSet.Find` method. A newly created book is then added to the author's Books collection navigation property. 
```csharp
using(var db = new TestContext())
{
    var book = new Book { Title = "King Lear" };
    var author = db.Authors.Find(1);
    author.Books.Add(book);
    db.SaveChanges();
}
```
This approach potentially results in two calls to the database. The `Find` method will first check the cache to see if an object of the same type with the same key value is being tracked by the context. If the author is not currently being tracked, a command is issued to select it from the database. Then a separate command inserts the book into the Books table:
```sql
exec sp_executesql N'SELECT TOP(1) [e].[AuthorId], [e].[FirstName], [e].[LastName]
FROM [Authors] AS [e]
WHERE [e].[AuthorId] = @__get_Item_0',N'@__get_Item_0 int',@__get_Item_0=1

exec sp_executesql N'SET NOCOUNT ON;
INSERT INTO [Books] ([AuthorId], [Title])
VALUES (@p0, @p1);
SELECT [BookId]
FROM [Books]
WHERE @@ROWCOUNT = 1 AND [BookId] = scope_identity();

',N'@p0 int,@p1 nvarchar(255)',@p0=1,@p1=N'King Lear'
```


## Attach a fake representing the principal then add the dependant to it

A "fake" or "stub" is used to represent the author in this example, which is then attached to the context. EF Core begins to track the fake author in the `Unchanged` state. When the book is added to the `Books` collection of the tracked author, the book's state is set to `Added`.

```csharp
using(var db = new TestContext())
{
    var book = new Book { Title = "As You Like It" };
    var author = new Author { AuthorId = 1 };
    db.Attach(author);
    author.Books.Add(book);
    db.SaveChanges();
}
```
This approach results in one database operation - inserting the new book into the database.
```sql
exec sp_executesql N'SET NOCOUNT ON;
INSERT INTO [Books] ([AuthorId], [Title])
VALUES (@p0, @p1);
SELECT [BookId]
FROM [Books]
WHERE @@ROWCOUNT = 1 AND [BookId] = scope_identity();

',N'@p0 int,@p1 nvarchar(255)',@p0=1,@p1=N'As You Like It'
```
### Set the Foreign Key value of the new book explicitly

Both of the previous examples offer a more object-oriented approach to creating relationships between entities. The next example has much more to do with working with relational data in a database. The book is created and has a valid value assigned to its foreign key property. There is no reference to an `Author` entity in this code:
```csharp
using(var db = new TestContext())
{
    var book = new Book { Title = "The Winter's Tale", AuthorId = 1 };
    db.Add(book);
    db.SaveChanges();
}
```
As with the previous approach, this results in just one database operation:
```sql
exec sp_executesql N'SET NOCOUNT ON;
INSERT INTO [Books] ([AuthorId], [Title])
VALUES (@p0, @p1);
SELECT [BookId]
FROM [Books]
WHERE @@ROWCOUNT = 1 AND [BookId] = scope_identity();

',N'@p0 int,@p1 nvarchar(255)',@p0=1,@p1=N'The Winter''s Tale'
```
## Modifying Relationships
The next examples look at modifying relationships between entities. 

### Use fakes to move the dependant to a different principal's collection 
This example results in an existing book with a key value of 4 being assigned to the author that has a key value of 1. Once again, fakes are attached to the context, telling the context that the entities with the specified keys already exist.

```csharp
using(var db = new TestContext())
{
    var book = new Book {BookId = 4};
    db.Attach(book);
    var author = new Author {AuthorId = 1};
    db.Attach(author);
    author.Books.Add(book);
    db.SaveChanges();
}
```
One database operation is generated, updating the foreign key value of the book.
```sql
exec sp_executesql N'SET NOCOUNT ON;
UPDATE [Books] SET [AuthorId] = @p0
WHERE [BookId] = @p1;
SELECT @@ROWCOUNT;

',N'@p1 int,@p0 int',@p1=4,@p0=1
```
### Alter the foreign key value of a fake
In this example, the foreign key value of the fake book is explicitly set.
```csharp
using(var db = new TestContext())
{
    var book = new Book { BookId = 4 };
    db.Attach(book);
    book.AuthorId = 2;
    db.SaveChanges();
}
```
Once again, only one database operation is generated.
```sql
exec sp_executesql N'SET NOCOUNT ON;
UPDATE [Books] SET [AuthorId] = @p0
WHERE [BookId] = @p1;
SELECT @@ROWCOUNT;

',N'@p1 int,@p0 int',@p1=4,@p0=2
```

### Add the dependant to the new principal's collection property
The `Find` method is used to obtain a reference to the book and the author it is to be assigned to.

```csharp
using(var db = new TestContext())
{
    var book = db.Books.Find(4);
    var author = db.Authors.Find(2);
    author.Books.Add(book);
    db.SaveChanges();
}
```
From a database operation point of view, this approach can be quite expensive, resulting in possibly three SQL commands being executed.
```sql
exec sp_executesql N'SELECT TOP(1) [e].[BookId], [e].[AuthorId], [e].[Title]
FROM [Books] AS [e]
WHERE [e].[BookId] = @__get_Item_0',N'@__get_Item_0 int',@__get_Item_0=4

exec sp_executesql N'SELECT TOP(1) [e].[AuthorId], [e].[FirstName], [e].[LastName]
FROM [Authors] AS [e]
WHERE [e].[AuthorId] = @__get_Item_0',N'@__get_Item_0 int',@__get_Item_0=2

exec sp_executesql N'SET NOCOUNT ON;
UPDATE [Books] SET [AuthorId] = @p0
WHERE [BookId] = @p1;
SELECT @@ROWCOUNT;

',N'@p1 int,@p0 int',@p1=4,@p0=2
```

## Removing relationships

### Delete the principal
Deleting the principal will ensure that the action specified by the Referential Constraint Action enumeration will be enforced. For required relationships, the dependants will all be deleted. If the relationship is optional, the foreign key values of the dependants will be set to null.

```csharp
using(var db = new TestContext())
{
    var authorToDelete = new Author { AuthorId = 1 };
    db.Authors.Remove(authorToDelete);
    db.SaveChanges();
}
```
```sql
exec sp_executesql N'SET NOCOUNT ON;
DELETE FROM [Authors]
WHERE [AuthorId] = @p0;
SELECT @@ROWCOUNT;

',N'@p0 int',@p0=2
```

### Set the Foreign Key value to null (optional relationships only)
If the relationship is optional, you can set the foreign key value to null to delete the relationship
```csharp
using(var db = new TestContext())
{
    var book = context.Books.Find(1);
    book.AuthorId = null;
    db.SaveChanges();
}
```
Again, the use of the `Find` method could result in two calls to the database
```sql
exec sp_executesql N'SELECT TOP(1) [e].[BookId], [e].[AuthorId], [e].[Title]
FROM [Books] AS [e]
WHERE [e].[BookId] = @__get_Item_0',N'@__get_Item_0 int',@__get_Item_0=1

exec sp_executesql N'SET NOCOUNT ON;
UPDATE [Books] SET [AuthorId] = @p0
WHERE [BookId] = @p1;
SELECT @@ROWCOUNT;

',N'@p1 int,@p0 int',@p1=1,@p0=NULL
```
You can also perform this operation using fakes and the DbContext.Entry:
```csharp
var book = new Book { BookId = 2 } ;
db.Attach(book);
book.AuthorId = null;
db.Entry(book).Property(p => p.AuthorId).IsModified = true;
db.SaveChanges();
```

```sql
exec sp_executesql N'SET NOCOUNT ON;
UPDATE [Books] SET [AuthorId] = @p0
WHERE [BookId] = @p1;
SELECT @@ROWCOUNT;

',N'@p1 int,@p0 int',@p1=2,@p0=NULL
```
### Remove the dependant from the principal's collection
You can remove the dependant from the principal's collection property. Note that this deletes the book in a required relationship, and sets the dependant's foreign key value to null in an optional relationship:
```csharp
using(var db = new TestContext())
{
    var book = db.Books.Find(1);
    var author = db.Authors.Find(1);
    author.Books.Remove(book);
    db.SaveChanges();
}
```
