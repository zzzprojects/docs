---
PermaID: 1000058
Name: Raw SQL Queries
---

# Raw SQL Queries

## Why Raw SQL Queries?

Entity Framework allows you to query using LINQ with your entity classes, but sometimes you want to run queries using raw SQL directly against the database. You will need raw SQL queries where you cannot use LINQ to represent the query, for example;

 - If the generated SQL is not efficient enough. 
 - If you want to make use of existing stored procedures
 - If you just prefer to write your queries in SQL.

## DbSet.SqlQuery 

The `DbSet.SqlQuery` method enables you to pass in a SQL command to be executed against the database to return instances of the type represented by the DbSet. 

```csharp
using (var context = new BookStore())
{        
    var books = context.Books
        .SqlQuery("SELECT * FROM Books")
        .ToList();
}
```

The returned objects will be tracked by the context just as they would be if they were returned by a LINQ query. 

[Try it](https://dotnetfiddle.net/4wUSHj)

## Database.SqlQuery

The `Database` class represents the underlying database and provides various methods to deal with the database. The `Database.SqlQuery()` method returns instances of any type, including primitive types.

```csharp
using (var context = new BookStore())
{        
    var books = context.Database
        .SqlQuery<string>("SELECT Title FROM Books")
        .ToList();
}
```

The results returned from `Database.SqlQuery` will never be tracked by the context even if the objects are instances of an entity type.

[Try it](https://dotnetfiddle.net/qxAXzb)

## Database.ExecuteSqlCommand

The `Database.ExecuteSqlCommnad` method is useful in executing non-query database commands, such as the Insert, Update and Delete command.

```csharp
using (var context = new BookStore())
{        
    var noOfRows = context.Database
        .ExecuteSqlCommand("UPDATE Books SET Title = 'Beginning C#' WHERE BookId = 1");
}
```

Any changes made to data in the database using ExecuteSqlCommand are opaque to the context until entities are loaded or reloaded from the database.

[Try it](https://dotnetfiddle.net/ool8DX)