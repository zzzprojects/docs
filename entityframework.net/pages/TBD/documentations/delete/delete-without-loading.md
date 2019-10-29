---
PermaID: 1000109
Name: Delete without Loading
---

# Delete Without Loading

## How to delete without loading entities in the context? 

Deleting entities using SaveChanges requires typically to load them first in the ChangeTracker. These additional round-trips are often not necessary.

### StackOverflow Related Questions

 - [Batch update/delete EF5](https://stackoverflow.com/questions/12751258/batch-update-delete-ef5)

## Answer

[Entity Framework Extensions](http://entityframework-extensions.net/) library adds the DeleteFromQuery extension method. **DeleteFromQuery** gives you access to directly execute a `DELETE` statement in the database and provide a **HUGE** performance improvement.

 - DELETE all rows from the database using a LINQ Query without loading entities in the context.
 - A DELETE statement is built using the LINQ expression and directly executed in the database.


```csharp
// DELETE all customers that are inactive
context.Customers.Where(c => !c.IsActive).DeleteFromQuery();

// DELETE customers by id
context.Customers.Where(x => x.CustomerID == customerId).DeleteFromQuery();
```

[Try it online](https://dotnetfiddle.net/8LgRzO)

### Performance Comparisons

|Operations	     |1,000 Entitie  |2,000 Entities |5,000 Entities|
|:-------------- |:------------- |:------------- |:------------ |
|SaveChange      |1,000 ms	     |2,000 ms	     |5,000 ms      |
|DeleteFromQuery |1 ms	         |1 ms	         |1 ms          |

[Learn more](http://entityframework-extensions.net/delete-from-query)