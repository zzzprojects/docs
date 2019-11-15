---
Permalink: ef6-query-db-set-filter-by-as-no-db-set-filter
---

# DbSet Filter - Query Filter AsNoDbSetFilter

You can bypass all filters by using AsNoDbSetFilter method in a query if a special scenario doesn't require filtering.

{% include template-example.html %} 
```csharp

// using Z.EntityFramework.Plus; // Don't forget to include this.
var ctx = new EntitiesContext();

this.DbSetFilter<Customer>(q => q.Where(x => x.IsActive));

// SELECT * FROM Customer WHERE IsActive = true
var list = ctx.Customers.ToList();

// SELECT * FROM Customer
var list = ctx.Customers.AsNoDbSetFilter().ToList();

```
[Try it](https://dotnetfiddle.net/ZIA1kt)
