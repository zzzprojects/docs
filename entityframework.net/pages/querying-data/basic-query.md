---
PermaID: 1000064
Title: Entity Framework Basic Query - Learn How To Make Basic Queries
MetaDescription: Unlock the power of Entity Framework by understanding what a LINQ query is and how to use it Learn how to get started by creating your first LINQ query and how to use it.
LastMod: 2025-06-17
Tags: query
---

# Entity Framework Basic Query: Discover How To Make Basic Queries

In Entity Framework, querying data is executed against the `DbSet` properties of the `DbContext`.

 - The `DbSet` and `IDbSet` implement IQueryable, so you can easily write a `LINQ` query against the database. 
 - LINQ is a component in the .NET Framework that provides query capability against collections in C# or VB. 
 - LINQ queries can be written using query syntax or method syntax. 
 - The EF provider is responsible for translating the LINQ query into the actual SQL to be executed against the database.

The following example loads all the data from `Books` table.

```csharp
using (var context = new BookStore())
{			
	var books = context.Books.ToList();
}
```

[Try it](https://dotnetfiddle.net/oo7OHH)

The following example loads a single record from `Books` table based on BookId.

```csharp
using (var context = new BookStore())
{
    var book = context.Books
        .Single(b => b.BookId == 1);
}
```

[Try it](https://dotnetfiddle.net/8Fs6jS)

The following example loads all books which contain C# in the title.

```csharp
using (var context = new BookStore())
{
    var books = context.Books
        .Where(b => b.Title.Contains("C#"))
        .ToList();
}
```

[Try it](https://dotnetfiddle.net/4TJtQl)
