---
title: EF Core DbSet
description: The Entity Framework Core DbSet class's role and capabilities  
canonical: /dbset
status: Published
lastmod: 2025-07-11
---

# EF Core DbSet

The `DbSet<TEntity>` class represents a collection for a given entity within the model and is the gateway to database operations against an entity. `DbSet<TEntity>` classes are added as properties to the `DbContext` and are mapped by default to database tables that take the name of the `DbSet<TEntity>` property. The `DbSet` is an implementation of the [Repository pattern](http://martinfowler.com/eaaCatalog/repository.html)

```csharp
public class SampleContext : DbContext
{
    public DbSet<Book> Books { get; set; }
    public DbSet<Author> Authors { get; set; }
}

public class Author
{
    public int AuthorId { get; set; }
    public string FirstName { get; set; }
    public string LastName { get; set; }

    public ICollection<Book> Books { get; set; }
}

public class Book
{    public int BookId { get; set; }
    public string Title { get; set; }
    public Author Author { get; set; }
    public int AuthorId { get; set; }
}
```

In the example above, two `DbSet<TEntity>` properties have been added to the `DbContext` class. The first represents a collection of `Book` objects, which is mapped by convention to a database table named "Books", after the property name. The second `DbSet` property represents a collection of `Author` objects, and is mapped to a table named "Authors".

## Basic operations

The `DbSet` object represents collections of entities _in memory_. Any changes you make to the contents of a `DbSet` will only be committed to the database if the `SaveChanges` method of the `DbContext` is called.

The `DbSet` class exposes several methods that enable you to perform basic CRUD (**C**reate, **R**ead, **U**pdate, **D**elete) operations against entities. 

### Adding an entity

To add a new entity to the collection represented by the `DbSet`, you use the `DbSet.Add` method:

```csharp
var author = new Author{
    FirstName = "William",
    LastName = "Shakespeare"
};
using (var context = new SampleContext())
{
    context.Authors.Add(author); // adds the author to the DbSet in memory
    context.SaveChanges(); // commits the changes to the database
}
```

### Retrieving an entity

If you wish to retrieve a single instance of an entity, you can use the `First` or `Single` method depending on whether you expect there to be more than one row matching the criteria.  The `Single` method will result in an exception being raised if more than one matching row exists. It should only be used when querying the context for entities by a unique key:

```csharp
using (var context = new SampleContext())
{
    var author = context.Authors.Single(a => a.AuthorId == 1);
}
``` 

If you are not certain of retrieving any data based on the criteria you pass in, you should use the `FirstOrDefault` or `SingleOrDefault` methods, which return `null` if no rows match the search criteria:

```csharp
using (var context = new SampleContext())
{
    var author = context.Authors.FirstOrDefault(a => a.LastName == "Shakespeare");
}
``` 

A convenience method called `Find` is available which is used to query the context for an entity by primary key value:

```csharp
using (var context = new SampleContext())
{
    var author = context.Authors.Find(1);
}
``` 

This method is a wrapper around the `SingleOrDefault` method and checks to see if an entity with the specified key is currently being tracked by the context, and if so, it will be returned. If not, a database query will be executed and the entity will be returned if found. If it isn't found, the `Find` method will return `null`. The `Single`, `First`, `SingleOrDefault`, and `FirstOrDefault` methods result in the immediate execution of a query against the database.

Asynchronous versions:`SingleAsync`, `FirstAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, `FindAsync`.

### Retrieving multiple entities

The most common method used to return multiple entities is the `ToList` method:

```csharp
using (var context = new SampleContext())
{
    var author = context.Authors.ToList();
}
``` 

### Modifying an entity

To update an entity, make the required changes to the value of the property or properties and call `SaveChanges`. The entity must be tracked by the context to be updated:

```csharp
using(var context = new SampleContext())
{
    var author = context.Authors.Find(1); // retrieve the entity
    author.LastName = "Jones"; // amend properties
    context.SaveChanges(); // commit the changes
}
```

Asynchronous version: `SaveChangesAsync`.

### Deleting an entity

The `DbSet` class `Remove` method is used to delete an entity. The entity must be tracked by the context to be removed:
 ```csharp
using(var context = new SampleContext())
{
    var author = context.Authors.Find(1); 
    context.Authors.Remove(author); 
    context.SaveChanges(); 
}
```

This approach may result in two SQL queries being executed: one generated by the `Single` method to select the entity if it isn't already being tracked by the context, and one generated by the `Remove` method to delete the entity.

You can instead use a __stub__ to prevent the need for a SQL query to retrieve the entity. A stub is a representation of the entity that is to be operated on. All that is required for a valid stub is the entity key value.

```csharp
using (var context = new SampleContext())
{
    var author = new Author { AuthorId = 1 };
    context.Authors.Attach(author);
    context.Authors.Remove(author);
    context.SaveChanges();
}
```

<!--DbSet.Update-->
