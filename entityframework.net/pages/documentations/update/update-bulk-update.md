---
PermaID: 1000036
Name: Bulk Update
---

# Bulk Update

## Definition
`UPDATE` all entities in the database.

All rows that match the entity key are considered as existing and are `UPDATED` in the database.

```csharp
// Easy to use
context.BulkUpdate(list);

// Easy to customize
context.BulkUpdate(customers, options => options.ColumnPrimaryKeyExpression = customer => customer.Code);
```
[Try it](https://dotnetfiddle.net/4JgSk3)

## Purpose

`Updating` entities using a custom key from file importation is a typical scenario.

Despite the `ChangeTracker` being outstanding to track what's modified, it lacks in term of scalability and flexibility.

`SaveChanges` requires one database round-trip for every entity to `update`. So if you need to `update` 10000 entities, then 10000 database round-trips will be performed which is **INSANELY** slow.

`BulkUpdate` in counterpart offers great customization and requires the minimum database round-trips possible.

## Performance Comparisons

| Operations      | 1,000 Entities | 2,000 Entities | 5,000 Entities |
| :-------------- | -------------: | -------------: | -------------: |
| SaveChanges     | 1,000 ms       | 2,000 ms       | 5,000 ms       |
| BulkUpdate      | 50 ms          | 55 ms          | 65 ms          |

[Read more...](https://entityframework-extensions.net/bulk-update)