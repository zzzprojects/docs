---
Name: Delete from Query
LastMod: 2025-06-23
---

# Delete from Query

## Definition
`DELETE` all rows from the database using a LINQ Query without loading entities in the context.

A `DELETE` statement is built using the LINQ expression and directly executed in the database.


```csharp
// DELETE all customers that are inactive
context.Customers.Where(x => !x.IsActive).DeleteFromQuery();

// DELETE customers by id
context.Customers.Where(x => x.ID == userId).DeleteFromQuery();
```

[Try it in EF Core](https://dotnetfiddle.net/nGIOTQ) | [Try it in EF6](https://dotnetfiddle.net/0my0fe)

## Purpose
`Deleting` entities using `SaveChanges` normally requires loading them first in the `ChangeTracker`. These additional round-trips are often not necessary.

`DeleteFromQuery` gives you access to directly execute a `DELETE` statement in the database and provides a **HUGE** performance improvement.

## Performance Comparisons

| Operations      | 1,000 Entities | 2,000 Entities | 5,000 Entities |
| :-------------- | -------------: | -------------: | -------------: |
| SaveChanges     | 1,000 ms       | 2,000 ms       | 5,000 ms       |
| DeleteFromQuery | 1 ms           | 1 ms           | 1 ms           |

{% include section-faq-begin.html %}
## FAQ

### Why DeleteFromQuery is faster than SaveChanges, BulkSaveChanges, and BulkDelete?

`DeleteFromQuery` executes a statement directly in SQL such as `DELETE FROM [TableName] WHERE [Key]`. 

Other operations normally require one or multiple database round-trips which makes the performance slower.
{% include section-faq-end.html %}