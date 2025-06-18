---
PermaID: 1000075
Title: Entity Framework Merge Records - Learn How to Upsert Entities
MetaDescription: Unlock the power of Entity Framework by learning how to merge/upsert your entities in the database.
LastMod: 2025-06-18
Tags: save merge bulk-merge
---

# Entity Framework Merge Records: Discover How to Upsert Entities

## How to Bulk Merge?

If you have data (list of entities) which have both new and old records. **SaveChanges** requires one database round-trip to check if the entity already exists and one round-trip to insert or update the entity. 

If you save 10,000 entities, 20,000 database round-trips will be performed which is **INSANELY** slow.  

### StackOverflow Related Questions

 - [MERGE in Entity Framework](https://stackoverflow.com/questions/5842125/merge-in-entity-framework?noredirect=1&lq=1)
 - [How can I use Entity Framework to do a MERGE when I don't know if the record exists?](https://stackoverflow.com/questions/23916453/how-can-i-use-use-entity-framework-to-do-a-merge-when-i-dont-know-if-the-record)

## Answer

[Entity Framework Extensions](https://entityframework-extensions.net/) library adds the BulkMerge extension method to the DbContext. **BulkMerge** offers great customization and requires the minimum database round-trips as compared to **SaveChanges**.


```csharp
// Easy to use
context.BulkMerge(list);

// Easy to customize
context.BulkMerge(customers, options => 
        options.ColumnPrimaryKeyExpression = customer => customer.Code);
```

[Try it online](https://dotnetfiddle.net/qUPpoc)

**MERGE** all entities from the database. A merge is an **UPSERT** operation, all rows that match the entity key are considered as existing and are **UPDATED**, other rows are considered as new rows and are **INSERTED** in the database.

### Performance Comparisons

|Operations	|1,000 Entities	|2,000 Entities	|5,000 Entities|
|:--------- |:------------- |:------------- |:------------ |
|SaveChange |1,000 ms	    |2,000 ms	    |5,000 ms      |
|BulkMerge	|65 ms	        |80 ms	        |110 ms        |

[Learn more](https://entityframework-extensions.net/bulk-merge)