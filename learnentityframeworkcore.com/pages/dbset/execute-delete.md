---
title: Deleting data via the ExecuteDelete
description: Deletes all database rows for the entity instances which match the LINQ query from the database. 
canonical: /dbset/execute-delete
status: Published
lastmod: 2023-02-21
---

# EF Core Execute Delete

By default, EF Core tracks changes to entities, and then sends updates to the database when one of the `SaveChanges` methods is called. 

 - Changes are only sent for properties and relationships that have changed. 
 - The tracked entities remain in sync with the changes sent to the database. 
 - It is an efficient and convenient way to send general-purpose inserts, updates, and deletes to the database. 
 - These changes are also batched to reduce the number of database round-trips.

However, it is sometimes useful to execute update or delete commands on the database without involving the change tracker. 

## DbSet.ExecuteDelete

EF Core 7.0 provides a new method called the `ExecuteDelete` method that deletes entities in the database based on the results of that query. 

 - The specific changes to make must be specified explicitly; they are not automatically detected by EF Core.
 - Any tracked entities will not be kept in sync.
 - Additional commands may need to be sent in the correct order so as not to violate database constraints. For example, deleting dependents before a principal can be deleted.
 - All of this means that the `ExecuteDelete` method complement, rather than replaces, the existing `SaveChanges` mechanism.

The following example shows how to call the `ExecuteDelete` on a `DbSet` and it will immediately delete all entities of that `DbSet` from the database. For example, to delete all Tag entities:

```csharp
using (var context = new LibraryContext())
{
    context.Authors.ExecuteDelete();
}
```

When `ExecuteDelete` is called, a `DELETE` statement is generated and executed by the database.

```csharp
DELETE FROM [a]
FROM [Authors] AS [a]
```

You can also use the query that contains a filter as shown in the following example.

```csharp
using (var context = new LibraryContext())
{
    context.Authors.Where(a => a.Name.Contains("John")).ExecuteDelete();
}
```

The above code will generate the following SQL statement.

```csharp
DELETE FROM [a]
FROM [Authors] AS [a]
WHERE [a].[Name] LIKE N'%John%'
```

You can also use more complex filters like including related data. For example, to delete tags only from old blog posts:

```csharp
using (var context = new BloggingContext())
{
    context.Tags
        .Where(t => t.Posts.All(e => e.PublishedOn.Year < 2018))
        .ExecuteDeleteAsync();
}
```

It will execute the following SQL statement.

```csharp
DELETE FROM [t]
FROM [Tags] AS [t]
WHERE NOT EXISTS (
    SELECT 1
    FROM [PostTag] AS [p]
    INNER JOIN [Posts] AS [p0] ON [p].[PostsId] = [p0].[Id]
    WHERE [t].[Id] = [p].[TagsId] AND NOT (DATEPART(year, [p0].[PublishedOn]) < 2018))
```
