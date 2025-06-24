---
Name: Coalesce Destination OnUpdate Expression
LastMod: 2025-06-23
---

# Coalesce Destination OnUpdate Expression

## Description

The `CoalesceDestinationOnUpdateExpression` is the inverse of `CoalesceOnUpdateExpression`. It allows you to update the new value if the database value is null otherwise, it keeps the database value when the `BulkUpdate` method is executed.

The following example will update only those columns for which the value is null in the database for the specified properties.

```csharp
using (var context = new EntityContext())
{
    var list = context.Customers.ToList();
    list.ForEach(x => { x.Name += "_Updated"; x.Description += "Add Description"; x.IsActive = false; });
			
    context.BulkUpdate(list, options => 
    {
        options.CoalesceDestinationOnUpdateExpression = c => new {c.CustomerID, c.Name, c.Description};
    });
}
```

Try it: [EF Core](https://dotnetfiddle.net/DVfHF4) | [EF6](https://dotnetfiddle.net/oJgbnd)

 - It will update the value in the `Description` column because the database value is null.
 - The value for `Name` is not null so it will not update the `Name` column.
 - The `IsActive` will also be updated, because it is not specified in `CoalesceDestinationOnUpdateExpression`.
