---
Permalink: ef-core-query-include-optimized-filter
---

# Query IncludeOptimized - Filter

Same as with EF+ Query IncludeFilter, it is possible to filter which related entities to load and then to launch a query.

{% include template-example.html %} 
```csharp

// using Z.EntityFramework.Plus; // Don't forget to include this.
var ctx = new EntitiesContext();

var orders = ctx.Orders.IncludeOptimized(x => x.Items.Where(y => y.IsActive));

```
[Try it](https://dotnetfiddle.net/Ws1CKx)

# Query IncludeOptimized - AllowQueryBatch

In some scenario, you may want to disable the Query Batch features to execute every query as individual.

{% include template-example.html %} 
```csharp

// using Z.EntityFramework.Plus; // Don't forget to include this.

QueryIncludeOptimizedManager.AllowQueryBatch = false;

```
