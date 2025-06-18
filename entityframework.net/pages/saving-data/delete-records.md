---
PermaID: 1000110
Title: Entity Framework Delete Records - Learn How to Delete your Entities
MetaDescription: Unlock the power of Entity Framework by understanding how to delete your records. Learn how to delete entities with savechanges or delete thousands of entities with bulk delete.
LastMod: 2025-06-18
Tags: saving delete
---

# Entity Framework Delete Records: Discover How to Delete your Entities

## How to Bulk Delete?

Deleting entities using a custom key from file importation is a typical scenario. Despite the ChangeTracker being outstanding to track what's modified, it lacks in terms of scalability and flexibility.

SaveChanges requires one database round-trip for every entity to delete. So if you need to remove 10000 entities, then 10000 database round-trips will be performed which is **INSANELY** slow.

### StackOverflow Related Questions

 - [Batch update/delete EF5](https://stackoverflow.com/questions/12751258/batch-update-delete-ef5)

## Answer

[Entity Framework Extensions](https://entityframework-extensions.net/) library adds the BulkDelete extension method to the DbContext. **BulkDelete** offers great customization and requires the minimum database round-trips as compared to **SaveChanges**.

```csharp
// Easy to use
context.BulkDelete(list);

// Easy to customize
context.BulkDelete(customers, options => 
        options.ColumnPrimaryKeyExpression = customer => customer.Code);
```
[Try it online](https://dotnetfiddle.net/gZiNGK)

All rows that match the entity key are considered as existing and are **DELETED** from the database.

### Performance Comparisons

|Operations	|1,000 Entities	|2,000 Entities	|5,000 Entities|
|:--------- |:------------- |:------------- |:------------ |
|SaveChanges |1,000 ms	    |2,000 ms	    |5,000 ms      |
|BulkDelete	|45 ms	        |50 ms	        |60 ms         |

[Learn more](https://entityframework-extensions.net/bulk-delete)