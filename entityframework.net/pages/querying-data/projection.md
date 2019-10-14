---
PermaID: 1000059
Name: Projection
---

# Projection

A projection is just a way of mapping one set of properties to another. In Entity Framework, it's a way of translating a full entity (database table) into a C# class with a subset of those properties.

 - Projection queries improve the efficiency of your application because only specific fields are retrieved from your database.
 - It is used to create a query that selects from a set of entities in your model but returns results that are of a different type.
 - When you have the data, you can project or filter it as you want the data before output.

## Anonymous Type

Anonymous types provide an easy way to create a new type without initializing them. In the following example, the projection query load the authors data into this Anonymous Type.

```csharp
var authors = context.Authors
    .Select(author => new
    {
    	Id = author.AuthorId,
    	FullName = author.FirstName + " " + author.LastName,
    	Books = author.Books
    }).ToList();
```

[Try it](https://dotnetfiddle.net/0tql9y)

## Concrete Type

You can also write a projection query to return a Concrete Type, and to do so we will have to create a custom class first, and it must have all properties, which we want to return from the table.

```csharp
public class CustomerData
{
    public int Id { get; set; }
    public string FullName { get; set; }
    public ICollection<Book> Books { get; set; }
}
```

The following projection query will map the result to the `AuthorDTO`.

```csharp
var authors = context.Authors
    .Select(author => new AuthorDTO
    {
    	Id = author.AuthorId,
    	FullName = author.FirstName + " " + author.LastName,
    	Books = author.Books
    }).ToList();
```

[Try it](https://dotnetfiddle.net/R3yQKp)