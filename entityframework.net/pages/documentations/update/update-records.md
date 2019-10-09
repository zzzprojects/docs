---
PermaID: 1000035
Name: Update Records
---

# Update Records

## How to Bulk Update?

Updating entities using a custom key from file importation is a typical scenario. Despite the ChangeTracker being outstanding to track what's modified, it lacks in term of scalability and flexibility.

SaveChanges requires one database round-trip for every entity to update. So if you need to update 10000 entities, then 10000 database round-trips will be performed which is **INSANELY** slow.

### StackOverflow Related Questions

 - [Batch update/delete EF5](https://stackoverflow.com/questions/12751258/batch-update-delete-ef5)
 - [EntityFrameWork Update Multiple Rows](https://stackoverflow.com/questions/19035312/entityframework-update-multiple-rows?noredirect=1&lq=1)

## Answer

[Entity Framework Extensions](http://entityframework-extensions.net/) library adds the BulkUpdate extension method to the DbContext. **BulkUpdate** offers great customization and requires the minimum database round-trips as compared to **SaveChanges**.


```csharp
// Easy to use
context.BulkUpdate(list);

// Easy to customize
context.BulkUpdate(customers, options => 
        options.ColumnPrimaryKeyExpression = customer => customer.Code);
```

[Try in online](https://dotnetfiddle.net/2PIVE8)

All rows that match the entity key are considered as existing and are UPDATED in the database.

### Performance Comparisons

|Operations	|1,000 Entities	|2,000 Entities	|5,000 Entities|
|:--------- |:------------- |:------------- |:------------ |
|SaveChange |1,000 ms	    |2,000 ms	    |5,000 ms      |
|BulkUpdate	|50 ms	        |55 ms	        |65 ms         |

[Learn more](http://entityframework-extensions.net/bulk-update)