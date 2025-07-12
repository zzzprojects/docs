---
title: Modifying data via the ExecuteUpdate
description: Updates all database rows for the entity instances which match the LINQ query from the database. 
canonical: /dbset/execute-update
status: Published
lastmod: 2025-07-11
---

# EF Core Execute Update

By default, EF Core tracks changes to entities, and then sends updates to the database when one of the `SaveChanges` methods is called. 

 - Changes are only sent for properties and relationships that have changed. 
 - The tracked entities remain in sync with the changes sent to the database. 
 - It is an efficient and convenient way to send general-purpose inserts, updates, and deletes to the database. 
 - These changes are also batched to reduce the number of database round-trips.

However, it is sometimes useful to execute update or delete commands on the database without involving the change tracker. 

## DbSet.ExecuteUpdate

EF Core 7.0 provides a new method called the `ExecuteUpdate` method that updates entities in the database based on the results of that query. 

 - The specific changes to make must be specified explicitly; they are not automatically detected by EF Core.
 - Any tracked entities will not be kept in sync.
 - Additional commands may need to be sent in the correct order so as not to violate database constraints. For example, update dependents before a principal can be deleted.
 - All of this means that the `ExecuteUpdate` method complements, rather than replaces, the existing `SaveChanges` mechanism.

The `ExecuteUpdate` behaves in a very similar way to the `ExecuteDelete` method. The main difference is that an update requires knowing which properties to update, and how to update them. This is achieved using one or more calls to `SetProperty`. 

The following example updates the `Name` property of every author.

```csharp
using (var context = new LibraryContext())
{
    context.Authors.ExecuteUpdate(
        s => s.SetProperty(b => b.Name, b => b.Name + " *Updated!*"));
}
```

The first parameter of `SetProperty` specifies which property to update; in this case, `Author.Name`. The second parameter specifies how the new value should be calculated; in this case, by taking the existing value and appending "*Updated!*". The resulting SQL is:

```csharp
UPDATE [a]
    SET [a].[Name] = [a].[Name] + N' *Updated!*'
FROM [Authors] AS [a]
```

It also allows you to use the query that can be used to filter which entities are updated. You can call the `SetProperty` method multiple times to update more than one property on the target entity. 

The following example updates the `Title` and `Content` of all the books published before 2018.

```csharp
using (var context = new LibraryContext())
{
    context.Books
        .Where(b => b.PublishedOn.Year < 2018)
        .ExecuteUpdateAsync(s => s
            .SetProperty(a => a.Title, a => a.Title + " (" + a.PublishedOn.Year + ")")
            .SetProperty(a => a.Content, a => a.Content + " ( This content was published in " + b.PublishedOn.Year + ")"));
}
```

In this case, the generated SQL is a bit more complicated:

```csharp
UPDATE [b]
    SET [b].[Content] = (([b].[Content] + N' ( This content was published in ') + COALESCE(CAST(DATEPART(year, [b].[PublishedOn]) AS nvarchar(max)), N'')) + N')',
    [b].[Title] = (([b].[Title] + N' (') + COALESCE(CAST(DATEPART(year, [b].[PublishedOn]) AS nvarchar(max)), N'')) + N')'
FROM [Books] AS [b]
WHERE DATEPART(year, [b].[PublishedOn]) < 2018
```

The `ExecuteDelete` also allows you to use the filter that can reference other tables. The following example updates all tags from old posts.

```csharp
using (var context = new BloggingContext())
{
    context.Tags
        .Where(t => t.Posts.All(e => e.PublishedOn.Year < 2022))
        .ExecuteUpdateAsync(s => s.SetProperty(t => t.Text, t => t.Text + " (old)"));
}
```

When the above code is executed, it will generate the following SQL statement. 

```csharp
UPDATE [t]
    SET [t].[Text] = [t].[Text] + N' (old)'
FROM [Tags] AS [t]
WHERE NOT EXISTS (
    SELECT 1
    FROM [PostTag] AS [p]
    INNER JOIN [Posts] AS [p0] ON [p].[PostsId] = [p0].[Id]
    WHERE [t].[Id] = [p].[TagsId] AND NOT (DATEPART(year, [p0].[PublishedOn]) < 2022))
```
