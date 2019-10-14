---
PermaID: 1000061
Name: Joining
IsUpdated: 1
---

# Joining

In SQL, a `JOIN` clause is used to combine data from two or more tables, based on a related column between them. Similarly, in Entity Framework, the LINQ `Join` is used to load data from two or more tables. 

 - It is always advisable to use navigational properties instead of LINQ `Join` to query the target data. 
 - But if the entities do not have any navigational properties defined on them, then you will need the `Join` operators 
 - It can also be used to fine-tune the generated queries for performance benefits.

The following query combines `Authors` and `Books` tables using the `Join()` method.

```csharp
using (var context = new BookStore())
{
    var data = context.Authors
        .Join(
            context.Books,
            author => author.AuthorId,
            book => book.Author.AuthorId,
            (author, book) => new
            {
                BookId = book.BookId,
                AuthorName = author.Name,
                BookTitle = book.Title
            }
        ).ToList();
	
    foreach(var book in data)
    {
        Console.WriteLine("Book Title: {0} \n\t Written by {1}", book.BookTitle, book.AuthorName);
    }
}
```

[Try it](https://dotnetfiddle.net/yXTgHu)

## Join Three Tables

You can also join more than two tables using LINQ `Join`. The following query combines the `Authors`, `AuthorBiographies` and `Books` tables using the `Join()` method.

```csharp
using (var context = new BookStore())
{
    var authorsData = context.Authors
        .Join(
            context.AuthorBiographies,
            author => author.AuthorId,
            authorBio => authorBio.AuthorBiographyId,
            (author, authorBio) => new
            {
                AuthorId = author.AuthorId,
                Name = author.Name,
                Biography = authorBio.Biography
            }
        )
        .Join(
            context.Books,
            author => author.AuthorId,
            book => book.BookId,
            (author, book) => new
            {
                AuthorId = author.AuthorId,
                Name = author.Name,
                Biography = author.Biography,
                BookTitle = book.Title
            }
        )
        .ToList();
```

[Try it](https://dotnetfiddle.net/mDcWVr)
