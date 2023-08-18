---
title: Deleting data via the DbContext
description: An examination of the methods and approaches available for deleting data via the Entity Framework Core DbContext API 
canonical: /dbcontext/deleting-data
status: Published
lastmod: 2023-02-22
---

# EF Core Delete Entity

The approach that you adopt to deleting entities via the `DbContext` depends on whether the context is currently tracking the entity being deleted or not. 

In the following example, the entity to be deleted is obtained by the context, so the context begins tracking it immediately. The `DbContext.Remove` method results in the entity's `EntityState` being set to `Deleted`. 

```csharp
context.Remove(context.Authors.Single(a => a.AuthorId == 1));
context.SaveChanges();
```
When `SaveChanges` is called, a `DELETE` statement is generated and executed by the database.

```sql
exec sp_executesql N'SET NOCOUNT ON;
DELETE FROM [Authors]
WHERE [AuthorId] = @p0;
SELECT @@ROWCOUNT;
',N'@p0 int',@p0=1
```
This approach results in _two_ SQL statements being executed: one to retrieve the entity from the database, and a second to delete it. You can use a _stub_ to represent the entity to be deleted and thereby stop the entity from being retrieved from the database:

```csharp
var context = new SampleContext();
var author = new Author { AuthorId = 1 };
context.Remove(author);
context.SaveChanges();
```
The only property that the stub requires is the primary key value.
## Setting EntityState

You can explicitly set the `EntityState` of an entity to `Deleted` via the `EntityEntry.State` property, which is made available by the `DbContext.Entry` method.

```csharp
var context = new SampleContext();
var author = new Author { AuthorId = 1 };
context.Entry(author).State = EntityState.Deleted;
context.SaveChanges();
```

## Related Data

If the entity that you want to delete has related data, the approach that you take will depend on how the relationship has been configured. A [fully defined relationship](/conventions/one-to-many-relationship#fully-defined-relationship) will have a cascading referential constraint set to  `Delete` or `SetNull`, as will a [relationship which has been configured via the Fluent API](/configuration/one-to-many-relationship-configuration#cascading-referential-integrity-constraints). In these cases, you can delete the principal and let the database take care of the dependent rows.

Where the referential constraint action is set to `NoAction`, you need to take care of any related data explicitly. The next example illustrates a relationship configured on a model that doesn't include a foreign key property:

 ```csharp
public class Author
{
    public int AuthorId { get; set; }
    public string FirstName { get; set; }
    public string LastName { get; set; }
    public ICollection<Book> Books { get; set; }
}

public class Book
{
    public int BookId { get; set; }
    public string Title { get; set; }
}
```
By default, this relationship is configured as optional and the [referential constraint action option](/relationships/referential-constraint-action-options) is configured to `NoAction`. In addition, EF Core introduces a [shadow property](/model/shadow-properties) to represent the foreign key. It is named `AuthorId` and is applied to the `Book` entity, and since the relationship is optional, the `AuthorId` property is nullable. To delete the author, you need to delete the relationship between each book and the author. The code to achieve this is as follows:

```csharp
var context = new SampleContext();
var author = context.Authors.Single(a => a.AuthorId == 1);
var books = context.Books.Where(b => EF.Property<int>(b, "AuthorId") == 1);
foreach (var book in books)
{
    author.Books.Remove(book);
}
context.Remove(author);
context.SaveChanges();
```
The author is retrieved from the database and then its books are obtained and removed one by one from the author's `Book` collection. Finally, the author is passed to the `Remove` method of the context. The result is that the books are updated so that their `AuthorId` value is modified to `null`:

```sql
exec sp_executesql N'SET NOCOUNT ON;
UPDATE [Books] SET [AuthorId] = @p0
WHERE [BookId] = @p1;
SELECT @@ROWCOUNT;
UPDATE [Books] SET [AuthorId] = @p2
WHERE [BookId] = @p3;
SELECT @@ROWCOUNT;
UPDATE [Books] SET [AuthorId] = @p4
WHERE [BookId] = @p5;
SELECT @@ROWCOUNT;
',N'@p1 int,@p0 int,@p3 int,@p2 int,@p5 int,@p4 int',@p1=1,@p0=NULL,@p3=2,@p2=NULL,@p5=3,@p4=NULL
```
and the author is deleted:
```sql
exec sp_executesql N'SET NOCOUNT ON;
DELETE FROM [Authors]
WHERE [AuthorId] = @p6;
SELECT @@ROWCOUNT;
',N'@p6 int',@p6=1
```
This approach results in _four_ calls being made to the database: one to select the author; one to select the books, one to update the books, and a final one to delete the author. It is therefore always a good idea to make use of the referential integrity constraints to set foreign keys to null or to delete dependents.
