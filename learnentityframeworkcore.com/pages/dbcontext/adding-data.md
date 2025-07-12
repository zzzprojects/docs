---
title: Adding data via the DbContext
description: Entity Framework Core provides the capability to add data directly via the DbContext class. 
canonical: /dbcontext/adding-data
status: Published
lastmod: 2025-07-11
---

# EF Core Insert Entity

The key methods for adding/inserting entities via the `DbContext` are

- `Add<TEntity>(TEntity entity)`
- `Add(object entity)`
- `AddRange(IEnumerable<object> entities)`
- `AddRange(params object[] entities)`

These methods are new to the `DbContext` in Entity Framework Core and have no equivalents in a previous version of Entity Framework where the `DbContext` is available (i.e. EF 4.1 onwards).

Most often, you will use the generic version of `Add` but omit the type parameter [because the compiler will infer the type from the argument passed into the method](https://msdn.microsoft.com/en-us/library/twcad0zb.aspx). The following two examples are identical:

```csharp
// with a type parameter
var author = new Author{ FirstName = "William", LastName = "Shakespeare" };
context.Add<Author>(author);
context.SaveChanges();

// without a type parameter
var author = new Author{ FirstName = "William", LastName = "Shakespeare" };
context.Add(author);
context.SaveChanges();
```
Visual Studio 2015 offers helpful advice to omit the type parameter in the first example. The second example should not be confused with the version of `Add` that takes an `object` type:

```csharp
object author = new Author{ FirstName = "William", LastName = "Shakespeare" };
context.Add(author);
context.SaveChanges();
```
When you use either version of `Add` the context begins tracking the entity that was passed into the method and applies an `EntityState` value of `Added` to it. The context also applies the same `EntityState` value of `Added` to all other objects in the graph that aren't already being tracked by the context. In the next example, the `Added` state is also applied to the books:

```csharp
var context = new SampleContext();
var author = new Author {
    FirstName = "William",
    LastName = "Shakespeare",
    Books = new List<Book>
    {
        new Book { Title = "Hamlet"},
        new Book { Title = "Othello" },
        new Book { Title = "MacBeth" }
    }
};
context.Add(author);
context.SaveChanges();
```
The books are added because they are referenced through the `Books` property of the author. In the next example, the books will **not** be added:
```csharp
var author = new Author { FirstName = "William", LastName = "Shakespeare" };
var hamlet = new Book { Title = "Hamlet", Author = author };
var othello = new Book { Title = "Othello", Author = author };
var macbeth = new Book { Title = "MacBeth", Author = author };
context.Add(author);
context.SaveChanges();
```
Although the author has been assigned to the `Author` property of each of the books that have been instantiated, the `author` entity is unaware of these relationships. Its `Books` property is still `null` and the books are not added to the context.

## EF Core Adding Multiple Records

The `AddRange` method is used for adding multiple objects to the database in one method call. The code in the next example is very similar to the previous example, but the `AddRange` method is used to save all the books _and_ the author to the database in one go:

```csharp
var context = new SampleContext();
var author = new Author { FirstName = "Stephen", LastName = "King" };
var books = new List<Book> {
    new Book { Title = "It", Author = author },
    new Book { Title = "Carrie", Author = author },
    new Book { Title = "Misery", Author = author }
};
context.AddRange(books);
context.SaveChanges();
```
This version of the `AddRange` method takes an `IEnumerable<object>`. EF Core is clever enough to identify the type of objects being added to the context and will form the appropriate SQL. The author is related to all of the books, so it forms part of the graph and is added too. 

The other version of the `AddRange` method takes a params array, and provides the facility to add several unrelated objects to the database in one go:

```csharp
var context = new SampleContext();
var author = new Author { FirstName = "William", LastName = "Shakespeare" };
var book = new Book { Title = "Adventures of Huckleberry Finn" };
context.AddRange(author, book);
context.SaveChanges();
```

When the `SaveChanges` method is called on the `DbContext`, all entities with an `EntityState` of `Added` will be inserted into the database.

#### Further Reading
- [Adding data via the DbSet](/dbset/adding-data)
