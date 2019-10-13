---
PermaID: 1000067
Name: Why Detect Changes is Slow
---

# Why DetectChanges is slow?

## Why Entity Framework DetectChanges is slow?

More entities your context own, slower the DetectChanges method become!

Entity Framework does a great job detect all changes for us, but in exchange, we pay the high-performance cost!

### Scenario:

Let look at this simple example to check the time taken when adding a different number of records, such as 1 record, 10, 100, 10000 records, etc.
 

```csharp
using (var ctx = new EF6.CustomerContext())
{
	for(int i = 0; i < lines.Count; i++)
	{
		ctx.Customers.Add(customer);
	}
}
```

Imagine the Add method is called every time you add a new entity.

#### Performance Comparisons

|Record #	        |Time to Add	|Total Time Spend   |
|:----------------- |:------------- |:----------------- |
|Record # 1     	|0 ms	        |0 ms               |
|Record # 10     	|0 ms	        |0 ms               |
|Record # 100       |0.1 ms	        |11 ms              |
|Record # 1,000     |1 ms	        |1,02 ms            |
|Record # 10,000    |21 ms	        |105,000 ms         |
|Record # 100,000   |210ms	        |3h                 |

***Note:***
 - **: SaveChanges time not included*
 - ***: Entity with two relations*

OMG! 3h to add 100,000 records to a list? 210 ms to add a single record once we reach 100,000 records and we didn't have saved them yet in the database.

That's very common on Stack Overflow someone asks why his importation take over 10h. Only by using AddRange over Add, they usually save more than 2h.