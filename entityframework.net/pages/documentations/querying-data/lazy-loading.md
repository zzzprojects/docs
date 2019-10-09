---
PermaID: 1000060
Name: Lazy Loading
---

# Lazy Loading

In Lazy loading, an entity or collection of entities is automatically loaded from the database when a property referring to that entity/entities is accessed for the first time. 

 - Lazy loading means delaying the loading of related data until you specifically request for it.
 - The related data is transparently loaded from the database when the navigation property is accessed.
 - It is enabled by default in Entity Framework, and you don't need to do anything. 

In the following example, the 'Author' entity contains the collection of `Books`. The context first loads the author entity data from the database, and then it will load the collection of books when we access the `Books` navigational property.

```csharp
using (var context = new BookStore())
{
    var author = context.Authors
        .FirstOrDefault();
    
    var books = author.Books;
}
```

When the above code is executed, it will result in two SQL queries. In the first query, it will fetch the first author from the database.

```csharp
SELECT TOP (1) 
    [c].[AuthorId] AS [AuthorId], 
    [c].[Name] AS [Name]
    FROM [dbo].[Authors] AS [c]
```

When the navigational property `Books` in `Author` class is accessed, it will send the following query to fetch all the books related to that author.

```csharp
SELECT 
    [Extent1].[Id] AS [Id], 
    [Extent1].[Title] AS [Title], 
    [Extent1].[AuthorId] AS [AuthorId]
    FROM [dbo].[Books] AS [Extent1]
    WHERE [Extent1].[AuthorId] = @EntityKeyValue1

-- EntityKeyValue1: '1' (Type = Int32, IsNullable = false)
```

[Try it](https://dotnetfiddle.net/yXTgHu)

## Disable Lazy Loading

Lazy loading is enabled by default, but you can disable lazy loading for a particular entity or a context. 

 - Do not make a navigational property virtual, if you want to turn off lazy loading for a particular property. 
 - If you want to turn off lazy loading for all entities in the context, set its configuration property to false.

```csharp
public BookStore() : base()
{
    this.Configuration.LazyLoadingEnabled = false;
}
```