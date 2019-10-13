---
PermaID: 1000034
Name: Update without loading
---

# Update Without Loading

## How to update without loading entities in the context?

Updating entities using SaveChanges requires typically to load them first in the ChangeTracker. These additional round-trips are often not necessary.

### StackOverflow Related Questions

 - [Update a record without first querying?](https://stackoverflow.com/questions/4218566/update-a-record-without-first-querying)
 - [Update record without fetching first Entity Framework](https://stackoverflow.com/questions/45938864/update-record-without-fetching-first-entity-framework?noredirect=1&lq=1)

## Answer

[Entity Framework Extensions](http://entityframework-extensions.net/) library adds the UpdateFromQuery extension method. **UpdateFromQuery** gives you access to directly execute an `UPDATE` statement in the database and provide a **HUGE** performance improvement.

 - UPDATE all rows from the database using a LINQ Query without loading entities in the context.
 - An UPDATE statement is built using the LINQ expression and directly executed in the database.


```csharp
// UPDATE all customers that are inactive for more than two years
context.Customers
    .Where(x => x.IsActive && x.LastLogin < DateTime.Now.AddYears(-2))
    .UpdateFromQuery(x => new Customer {IsActive = false});
	
// UPDATE customers by id
context.Customers
    .Where(x => x.CustomerID == userId)
    .UpdateFromQuery(x => new Customer {IsActive = false});
```
[Try it online](https://dotnetfiddle.net/9hAsuQ)

### Performance Comparisons

|Operations	     |1,000 Entitie  |2,000 Entities |5,000 Entities|
|:-------------- |:------------- |:------------- |:------------ |
|SaveChange      |1,000 ms	     |2,000 ms	     |5,000 ms      |
|UpdateFromQuery |1 ms	         |1 ms	         |1 ms          |

[Learn more](http://entityframework-extensions.net/update-from-query)