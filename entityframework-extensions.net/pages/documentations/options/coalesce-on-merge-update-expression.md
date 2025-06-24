---
Name: Coalesce OnMergeUpdate Expression
LastMod: 2025-06-23
---

# Coalesce OnMergeUpdate Expression

## Description

The `CoalesceOnMergeUpdateExpression` allows you to not update any column if the specified value is `null` and its database value is not null when `BulkMerge` method is executed.

The following example will update only those columns in which the specified value is not null.

```csharp
using (var context = new EntityContext())
{
    var list = context.Customers.ToList();
    list.ForEach(x => { x.Name += "_Updated"; x.Description = null; x.IsActive = false;});
    list.Add(new Customer() { Name ="Customer_C", Description = null, IsActive = false});
    
    context.BulkMerge(list, options => 
    {
        options.CoalesceOnMergeUpdateExpression = c => new {c.CustomerID, c.Description};
    });                  
}
```

Try it: [EF Core](https://dotnetfiddle.net/Sy3rfx) | [EF6](https://dotnetfiddle.net/JOrTfP)

 - It will update only `Name` and `IsActive` columns because the new specified values are not null.
 - The new value for `Description` is null so it will not update the `Description` column.
