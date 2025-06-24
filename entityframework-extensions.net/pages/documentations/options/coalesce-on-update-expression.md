---
Name: Coalesce OnUpdate Expression
LastMod: 2025-06-23
---

# Coalesce OnUpdate Expression

## Description

The `CoalesceOnUpdateExpression` allows you to not update any column if the specified value is `null` and its database value is not null when `BulkUpdate` method is executed.

The following example will update only those columns in which the specified value is not null.

```csharp
using (var context = new EntityContext())
{
    var list = context.Customers.ToList();
    list.ForEach(x => { x.Name += "_Updated"; x.Description = null; x.IsActive = false;});
    
    context.BulkUpdate(list, options => 
    {
        options.CoalesceOnUpdateExpression = c => new {c.CustomerID, c.Description};
    });                  
}
```

Try it: [EF Core](https://dotnetfiddle.net/WRNrqj) | [EF6](https://dotnetfiddle.net/lJagVr)

 - It will update only `Name` and `IsActive` columns because the new specified values are not null.
 - The new value for `Description` is null so it will not update the `Description` column.
