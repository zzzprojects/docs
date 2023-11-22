---
title: Adding data via the DbSet
description: An examination of the methods and approaches available for adding data via the Entity Framework Core DbSet API 
canonical: /dbset/adding-data
status: Published
lastmod: 2023-11-22
---

# EF Core Add Record

The key methods for adding entities via the `DbSet` are

- `Add<TEntity>(TEntity entity)`
- `AddRange<TEntity>(IEnumerable<TEntity> entities)`
- `AddRange<TEntity>(params TEntity[] entities)`

Most often, you will see examples of the generic version of `Add` but with the type, parameter omitted [because the compiler will infer it](https://msdn.microsoft.com/en-us/library/twcad0zb.aspx). The following two examples are identical:

```csharp
// with the type parameter
var author = new Author{ FirstName = "William", LastName = "Shakespeare" };
context.Authors.Add<Author>(author);

// without a type parameter
var author = new Author{ FirstName = "William", LastName = "Shakespeare" };
context.Authors.Add(author);
```

The context begins tracking the entity that was passed into the `Add` method and applies an `EntityState` value of `Added` to it. The context also applies the same `EntityState` value of `Added` to all other objects in the object graph that aren't already being tracked by the context. In the next example, the `Added` state is also applied to the books:

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
context.Authors.Add(author);

```

The books are added because they are referenced through the `Books` property of the author. In the next example, the books will **not** be added:

```csharp
var author = new Author { FirstName = "William", LastName = "Shakespeare" };
var hamlet = new Book { Title = "Hamlet", Author = author };
var othello = new Book { Title = "Othello", Author = author };
var macbeth = new Book { Title = "MacBeth", Author = author };
context.Authors.Add(author);
context.SaveChanges();
```

Although the author has been assigned to the `Author` property of each of the books that have been instantiated, the `author` entity is unaware of these relationships. Its `Books` property is still `null` and the books are not added to the context.

## Adding Multiple Records

The `AddRange` method is used for adding multiple objects to the database in one method call. The code in the next example is very similar to the previous example, but the `AddRange` method is used to save all the books _and_ the author to the database in one go:

```csharp
var context = new SampleContext();
var author = new Author { FirstName = "Stephen", LastName = "King" };
var books = new List<Book> {
    new Book { Title = "It", Author = author },
    new Book { Title = "Carrie", Author = author },
    new Book { Title = "Misery", Author = author }
};
context.Books.AddRange(books);
context.SaveChanges();
```

The `AddRange` method takes an `IEnumerable<object>`. The author forms part of the object graph for at least one of the books, so it is added too. 

The other version of the `AddRange` method takes a params array of entities, providing the facility to add a variable number of entities to the database without the need to create a collection for them:

```csharp
var context = new SampleContext();
var author = new Author { FirstName = "William", LastName = "Shakespeare" };
var hamlet = new Book { Title = "Hamlet", Author = author };
var othello = new Book { Title = "Othello", Author = author };
var macbeth = new Book { Title = "MacBeth", Author = author };
context.Books.AddRange(hamlet, othello, macbeth);
context.SaveChanges();
```

When the `SaveChanges` method is called on the `DbContext`, all entities with an `EntityState` of `Added` will be inserted into the database. The ordering of the SQL to insert objects is managed in such a way to ensure that principals are inserted first and their primary key value is then available to be applied to the foreign key of dependent objects. 

#### Further Reading

- [Adding data via the DbContext](/dbcontext/adding-data)
- [EF Core - Bulk Insert](https://entityframework-extensions.net/bulk-insert)
