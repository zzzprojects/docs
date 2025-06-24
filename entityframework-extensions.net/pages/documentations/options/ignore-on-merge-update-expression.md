---
Name: Ignore OnMergeUpdate Expression
LastMod: 2025-06-24
---

# Ignore OnMergeUpdate Expression

## Description

The `IgnoreOnMergeUpdateExpression` allows you to ignore some columns when the `BulkMerge` method executes the `update` statement, and these columns will only be used in the `insert` statement.

The following example ignores the `Name` and `IsActive` properties while updating the records and will be considered during insertion.


```csharp
using (var context = new EntityContext())
{
    var list = context.Customers.ToList();
    list.ForEach(x => 
    { 
        x.IsActive = false; 
	x.Name += "_NotMerge"; 
	x.Description += "_Merge"; 
	x.IsActive = false; 
    });
			
    context.BulkMerge(list, options => 
    {
        options.IgnoreOnMergeUpdateExpression = customer => new 
	{
	    customer.CustomerID, 
	    customer.IsActive, 
	    customer.Name
	};
    });
}
```
Try it: [EF Core](https://dotnetfiddle.net/Spifzy) | [EF6](https://dotnetfiddle.net/Z0xM1L)
