---
PermaID: 1000067
Title: Entity Framework DetectChanges - Learn Why it is Slow
MetaDescription: Unlock the power of Entity Framework by understanding why the DetectChanges method is slow. Learn the secret of why the method can take hours to add entities in the context.
LastMod: 2025-06-18
Tags: saving performance
---

# Entity Framework DetectChanges: Discover Why it is Slow

## Why Entity Framework DetectChanges is slow?

The more entities your context owns, slower the DetectChanges method becomes!

Entity Framework does a great job of detecting all changes for us, but in exchange, we pay the high-performance cost!

### Scenario:

Let's look at this simple example to check the time taken when adding a different number of records, such as 1 record, 10, 100, 10000 records, etc.
 

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

|Record #	        |Time to Add	|Total Time Spent   |
|:----------------- |:------------- |:----------------- |
|Record # 1     	|0 ms	        |0 ms               |
|Record # 10     	|0 ms	        |0 ms               |
|Record # 100       |0.1 ms	        |11 ms              |
|Record # 1,000     |1 ms	        |1,020 ms           |
|Record # 10,000    |21 ms	        |105,000 ms         |
|Record # 100,000   |210 ms	        |3 hours            |

***Note:***
 - **: SaveChanges time not included*
 - ***: Entity with two relations*

OMG! 3 hours to add 100,000 records to a list? 210 ms to add a single record once we reach 100,000 records and we haven't saved them yet in the database.

That's very common on Stack Overflow someone asks why their import takes over 10 hours. Only by using AddRange over Add, they usually save more than 2 hours.