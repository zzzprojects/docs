---
PermaID: 1000126
Title: Entity Framework ConcurrencyCheck Data Annotations
MetaDescription: Unlock the power of Entity Framework by using the ConcurrencyCheck Annotations. Learn What a concurrency check and how to specify this attribute is.
LastMod: 2023-02-24
Tags: code-first fluent-api data-annotations
---

# Entity Framework ConcurrencyCheck Data Annotations

The `ConcurrencyCheck` attribute allows you to configure one or more properties to be used for concurrency checking in the database when a user edits or deletes an entity. 

 - You can use `ConcurrencyCheck` attribute when you want to use existing columns for concurrency check.
 - It is used to specify that a property should be included in a `WHERE` clause in an `UPDATE` or `DELETE` statement.

In the following example, the `ConcurrencyCheck` attribute is applied to the `Title` property in `Book` class.

```csharp
public class Book
{
    public int BookId { get; set; }
    [ConcurrencyCheck]
    public string Title { get; set; }
}
```

Code First will include `Title` column in update command to check for optimistic concurrency.

```csharp
UPDATE [Books]
SET [Title] = @0
WHERE (([BookId] = @1) AND ([Title] = @2))
-- @0: 'My Book updated title' (Type = String)
-- @1: '1' (Type = Int32)
-- @2: 'My Book' (Type = String)
```

[Try it](https://dotnetfiddle.net/v1mHtl)