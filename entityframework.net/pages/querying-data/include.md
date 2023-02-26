---
PermaID: 1000062
Title: Entity Framework Include - Learn to Specify Related Entities
MetaDescription: Unlock the power of Entity Framework by including related entities in your LINQ query. Learn how the Include method allows you to load more data in the same query.
LastMod: 2023-02-26
Tags: query include
---

# Entity Framework Include: Discover to Specify Related Entities

In Entity Framework, the `Include` method loads the related objects to include in the query results. It can be used to retrieve some information from the database and also want to include related entities. 

We have a simple model which contains two entities.

```csharp
public class Book
{
    public int Id { get; set; }
    public string Title { get; set; }
    public int AuthorId { get; set; }
    [ForeignKey("AuthorId")]
    public Author Author { get; set; }
}

public class Author
{
    public int AuthorId { get; set; }
    public string Name { get; set; }
    public ICollection<Book> Books { get; set; }
}
```

## Lambda Expression

The following LINQ query loads all authors, and their related books using the lambda expression.

```csharp
using (var context = new BookStore())
{
    var authors = context.Authors
        .Include(a => a.Books)
        .ToList();
}
```

[Try it](https://dotnetfiddle.net/jsrNFd)

## String Path

You can also use a string path to specify the relationships to load all authors and their related books.

```csharp
using (var context = new BookStore())
{
    var authors = context.Authors
        .Include("Books")
        .ToList();
}
```

[Try it](https://dotnetfiddle.net/rSAVjT)