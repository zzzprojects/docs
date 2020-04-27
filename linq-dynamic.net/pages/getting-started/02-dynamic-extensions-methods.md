---
PermaID: 101001
Name: Dynamic Extensions Methods
---

# Dynamic Extensions Methods

The DynamicQueryableExtensions class implements extension methods for dynamically querying objects that implement the IQueryable<T> interface. These methods correspond to their System.Linq.Queryable counterparts, except that they operate on IQueryable instead of IQueryable<T> and use strings instead of lambda expressions to express predicates, selectors, and orderings.

```csharp
using (var context = new EntityContext())
{
    var query =  context.Customers.
        Where("Orders.Count >= @0", 5).
        OrderBy("Orders.Count").
        Select("new (Name, Phone)");
}
```

You can see that string expressions are used in the query and that could have been dynamically constructed at run-time.

## Supported Methods

You can use string expressions in any of the following methods while creating a query dynamically.

 - All
 - Any 
 - Average
 - Count
 - First
 - FirstOrDefault
 - GroupBy
 - GroupByMany
 - GroupJoin
 - Join
 - Last
 - LastOrDefault
 - LongCount
 - OfType
 - OrderBy
 - Select
 - SelectMany
 - Single
 - SingleOrDefault
 - Sum
 - ThenBy
 - Where
