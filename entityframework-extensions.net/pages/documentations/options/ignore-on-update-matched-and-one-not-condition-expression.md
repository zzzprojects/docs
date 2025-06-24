---
Name: Ignore OnUpdateMatched AndOneNotCondition Expression
LastMod: 2025-06-24
---

# Ignore OnUpdateMatched AndOneNotCondition Expression

## Description

The `IgnoreOnUpdateMatchedAndOneNotConditionExpression` is the inverse of `MergeMatchedAndNotConditionExpression` 

 - The `UpdateMatchedAndOneNotConditionExpression` allows you to perform the bulk update operation if the specified property value is not equal to the database value.
 - So by default, all columns are included in `IgnoreOnUpdateMatchedAndOneNotConditionExpression` except the ones you choose to ignore.

The following example updates all those records in which the `ModifiedDate` property is not equal to a database value. It ignores the specified properties in `IgnoreOnUpdateMatchedAndOneNotConditionExpression` if their value is equal to the database or not.

```csharp
using (var context = new EntityContext())
{
    var customers = context.Customers.ToList();
    customers.ForEach(x => { x.Name += "_Updated"; x.Description += "_Updated"; x.IsActive = false; });
    customers.Take(2).ToList().ForEach(x => { x.ModifiedDate = DateTime.Now; });

    context.BulkUpdate(customers, options => 
    {
        options.IgnoreOnUpdateMatchedAndOneNotConditionExpression = c => new 
        {
            c.Name, 
            c.Description, 
            c.CreatedDate, 
            c.IsActive 
        };
    });
}
```

Try it: [EF Core](https://dotnetfiddle.net/cbtsCP) | [EF6](https://dotnetfiddle.net/5v8F8E)

 - It will update all the records except for the last record.
 - The `ModifiedDate` property is only updated for the first two records and not for the last record.
