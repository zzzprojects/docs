---
PermaID: 1000063
Name: Eager Loading
---

# Eager Loading

In Eager loading, a query for one type of entity also loads related entities as part of the query. 

 - Using eager loading, you don't need to execute a separate query for related entities.
 - It means that requesting related data be returned along with query results from the database. 
 - It can be achieved by using the `Include` method.

In the following example, all the authors with their respective books are retrieved from the database by using eager loading.

```csharp
using (var context = new BookStore())
{
    var authors = context.Authors
        .Include(a => a.Books)
        .ToList();
}
```

The authors and their related books will be retrieved in a single query.

```csharp
SELECT 
    [Project1].[AuthorId] AS [AuthorId], 
    [Project1].[Name] AS [Name], 
    [Project1].[C1] AS [C1], 
    [Project1].[Id] AS [Id], 
    [Project1].[Title] AS [Title], 
    [Project1].[AuthorId1] AS [AuthorId1]
    FROM ( SELECT 
        [Extent1].[AuthorId] AS [AuthorId], 
        [Extent1].[Name] AS [Name], 
        [Extent2].[Id] AS [Id], 
        [Extent2].[Title] AS [Title], 
        [Extent2].[AuthorId] AS [AuthorId1], 
        CASE WHEN ([Extent2].[Id] IS NULL) THEN CAST(NULL AS int) ELSE 1 END AS [C1]
        FROM  [dbo].[Authors] AS [Extent1]
        LEFT OUTER JOIN [dbo].[Books] AS [Extent2] ON [Extent1].[AuthorId] = [Extent2].[AuthorId]
    )  AS [Project1]
    ORDER BY [Project1].[AuthorId] ASC, [Project1].[C1] ASC
```

[Try it](https://dotnetfiddle.net/Kjlh5E)