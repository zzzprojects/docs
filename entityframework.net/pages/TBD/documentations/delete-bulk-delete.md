---
PermaID: 1000112
Title: Entity Framework Bulk Delete - Learn How to Improve Delete Performance 
MetaDescription: Unlock the power of Entity Framework by learning how to bulk delete your entities in the database.
LastMod: 2025-06-18
Tags: save delete bulk-delete
---

# Entity Framework Bulk Delete: Discover How to Improve Delete Performance

## Definition
`DELETE` all entities from the database.

All rows that match the entity key are `DELETED` from the database.


```csharp
// Easy to use
context.BulkDelete(list);

// Easy to customize
context.BulkDelete(customers, options => options.ColumnPrimaryKeyExpression = customer => customer.Code);
```
[Try it](https://dotnetfiddle.net/j2OgnK)

## Purpose
`Deleting` entities using a custom key from file importation is a typical scenario.

Despite the `ChangeTracker` being outstanding to track what's modified, it lacks in terms of scalability and flexibility.

`SaveChanges` requires one database round-trip for every entity to `delete`. So if you need to `delete` 10000 entities, then 10000 database round-trips will be performed which is **INSANELY** slow.

`BulkDelete`, on the other hand, offers great customization and requires the fewest database round-trips possible.

## Performance Comparisons

| Operations      | 1,000 Entities | 2,000 Entities | 5,000 Entities |
| :-------------- | -------------: | -------------: | -------------: |
| SaveChanges     | 1,000 ms       | 2,000 ms       | 5,000 ms       |
| BulkDelete      | 45 ms          | 50 ms          | 60 ms          |

[Read more...](https://entityframework-extensions.net/bulk-delete)