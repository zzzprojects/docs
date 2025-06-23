---
Name: Update from Query
LastMod: 2025-06-23
---

# Update from Query

## Definition
`UPDATE` all rows from the database using a LINQ Query without loading entities in the context.

An `UPDATE` statement is built using the LINQ expression and directly executed in the database.


```csharp
// UPDATE all customers that are inactive for more than two years
var date = DateTime.Now.AddYears(-2);
context.Customers
    .Where(x => x.IsActive && x.LastLogin < date)
    .UpdateFromQuery(x => new Customer {IsActive = false});
	
// UPDATE customers by id
context.Customers.Where(x => x.ID == userId).UpdateFromQuery(x => new Customer {IsActive = false});
```

[Try it in EF Core](https://dotnetfiddle.net/ASfpQe) | [Try it in EF6](https://dotnetfiddle.net/9hAsuQ)

## Purpose
`Updating` entities using `SaveChanges` normally requires loading them first in the `ChangeTracker`. These additional round-trips are often not necessary.

`UpdateFromQuery` gives you access to directly execute an `UPDATE` statement in the database and provides a **HUGE** performance improvement.

## Performance Comparisons

| Operations      | 1,000 Entities | 2,000 Entities | 5,000 Entities |
| :-------------- | -------------: | -------------: | -------------: |
| SaveChanges     | 1,000 ms       | 2,000 ms       | 5,000 ms       |
| UpdateFromQuery | 1 ms           | 1 ms           | 1 ms           |

{% include section-faq-begin.html %}
## FAQ

### Why UpdateFromQuery is faster than SaveChanges, BulkSaveChanges, and BulkUpdate?

`UpdateFromQuery` executes a statement directly in SQL such as `UPDATE [TableName] SET [SetColumnsAndValues] WHERE [Key]`. 

Other operations normally require one or multiple database round-trips which makes the performance slower.
{% include section-faq-end.html %}