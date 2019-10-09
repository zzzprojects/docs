---
PermaID: 1000079
Name: LINQ Queries
---

# LINQ Queries

A query is an expression that retrieves data from a data source. Queries are usually expressed in a specialized query language, such as SQL for relational databases and XQuery for XML. 

 - Language-Integrated Query (LINQ) offers a simpler, consistent model for working with data across various kinds of data sources and formats. 
 - In a LINQ query, you always work with programming objects.

LINQ to Entities queries can be composed in two different syntaxes: 

 - **Query Expression Syntax:** Query expressions are a declarative query syntax. This syntax enables a developer to write queries in a high-level language that is formatted similar to Transact-SQL.
 - **Method-based Query Syntax:** Another way to compose LINQ to Entities queries is by using method-based queries. The method-based query syntax is a sequence of direct method calls to LINQ operator methods, passing lambda expressions as the parameters. 

## Select

Projects each element of a sequence into a new form. 

```csharp
using (var context = new BookStore())
{
    var authors = context.Authors
        .Select(author => new
        {
            Id = author.AuthorId,
            FullName = author.FirstName + " " + author.LastName,
            Books = author.Books
        }).ToList();
}
```

[Try it](https://dotnetfiddle.net/hV2x8J)

## SelectMany

Projects each element of a sequence to an `IEnumerable` and combines the resulting sequences into one sequence.

```csharp
using (var context = new BookStore())
{
    var allBooks = context.Authors
        .SelectMany(a => a.Books).ToList();
}
```

[Try it](https://dotnetfiddle.net/3ZVUpE)

## Where

Filters a sequence of values based on a predicate. Each element's index is used in the logic of the predicate function.

```csharp
using (var context = new BookStore())
{
    var allBooks = context.Books
        .Where(b => b.Title.Contains("Fundamentals"))
        .ToList();
}
```

[Try it](https://dotnetfiddle.net/CBntuc)

## OrderBy

Sorts the elements of a sequence in ascending order by using a specified comparer.

```csharp
using (var context = new BookStore())
{
    var allBooks = context.Books
        .OrderBy(b => b.Title)
        .ToList();
}
```

[Try it](https://dotnetfiddle.net/VOV7m5)

## OrderByDescending

Sorts the elements of a sequence in descending order by using a specified comparer.

```csharp
using (var context = new BookStore())
{
    var allBooks = context.Books
        .OrderByDescending(b => b.Title)
        .ToList();
}
```

[Try it](https://dotnetfiddle.net/589TDD)

## ThenBy

Performs a subsequent ordering of the elements in a sequence in ascending order by using a specified comparer.

```csharp
using (var context = new BookStore())
{
    var authors = context.Authors
        .OrderBy(a => a.LastName)
        .ThenBy(a => a.FirstName)
        .ToList();
}
```

[Try it](https://dotnetfiddle.net/Hn4KrH)

## ThenByDescending

Performs a subsequent ordering of the elements in a sequence in descending order by using a specified comparer.

```csharp
using (var context = new BookStore())
{
    var authors = context.Authors
        .OrderBy(a => a.LastName)
        .ThenByDescending(a => a.FirstName)
        .ToList();
}
```

[Try it](https://dotnetfiddle.net/WfS4Hb)

## Skip

Bypasses a specified number of elements in a sequence and then returns the remaining elements.

```csharp
using (var context = new BookStore())
{
    var authors = context.Authors
        .OrderBy(a => a.FirstName)
        .ThenBy(a => a.LastName)
        .Skip(2)
        .ToList();
}
```

[Try it](https://dotnetfiddle.net/O2rtoe)

## Take

Returns a specified number of contiguous elements from the start of a sequence.

```csharp
using (var context = new BookStore())
{
    var authors = context.Authors
        .OrderBy(a => a.FirstName)
        .ThenBy(a => a.LastName)
        .Take(2)
        .ToList();
}
```

[Try it](https://dotnetfiddle.net/n5a7oD)

## First

Returns the first element of a sequence.

```csharp
using (var context = new BookStore())
{
    var author = context.Authors
        .First();
}
```

[Try it](https://dotnetfiddle.net/nkQrSs)

## FirstOrDefault

Returns the first element of a sequence that satisfies a specified condition or a default value if no such element is found.

```csharp
using (var context = new BookStore())
{
    var author = context.Authors
        .FirstOrDefault();
}
```

[Try it](https://dotnetfiddle.net/OQHCRT)

## Single

Returns the only element of a sequence that satisfies a specified condition, and throws an exception if more than one such element exists.

```csharp
using (var context = new BookStore())
{
    var author = context.Authors
        .Single(a => a.AuthorId == 2);
}
```

[Try it](https://dotnetfiddle.net/MVf7Qg)

## SingleOrDefault

Returns the first element of a sequence that satisfies a specified condition or a default value if no such element is found.

```csharp
using (var context = new BookStore())
{
    var author = context.Authors
        .SingleOrDefault(a => a.AuthorId == 2);
}
```

[Try it](https://dotnetfiddle.net/SI32Bt)

## Any

You can use the **Any** method to determines whether a sequence contains any elements.

```csharp
using (var context = new BookStore())
{
    var author = context.Authors
        .Where(a => a.Books.Any()).ToList();
}
``` 

[Try it](https://dotnetfiddle.net/KVAUjv)