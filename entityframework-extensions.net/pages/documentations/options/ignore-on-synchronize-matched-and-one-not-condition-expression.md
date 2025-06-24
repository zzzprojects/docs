---
Name: Ignore OnSynchronizeMatched AndOneNotCondition Expression
LastMod: 2025-06-24
---

# Ignore OnSynchronizeMatched AndOneNotCondition Expression

## Description

The `IgnoreOnSynchronizeMatchedAndOneNotConditionExpression` is the inverse of `SynchronizeMatchedAndNotConditionExpression` 

 - The `SynchronizeMatchedAndOneNotConditionExpression` allows you to perform the bulk synchronize operation if the specified property value is not equal to the database value.
 - So by default, all columns are included in `IgnoreOnSynchronizeMatchedAndOneNotConditionExpression` except the ones you choose to ignore.

The following example updates all those records in which the `ModifiedDate` property is not equal to a database value. It ignores the specified properties in `IgnoreOnSynchronizeMatchedAndOneNotConditionExpression` if their value is equal to the database or not.

```csharp
using (var context = new EntityContext())
{
    var customers = context.Customers.ToList();
    customers.ForEach(x => { x.Name += "_Updated"; x.Description += "_Updated"; x.IsActive = false; });
    customers.Take(2).ToList().ForEach(x => { x.ModifiedDate = DateTime.Now; });

    context.BulkSynchronize(customers, options => 
    {
        options.IgnoreOnSynchronizeMatchedAndOneNotConditionExpression = c => new 
        {
            c.Name, 
            c.Description, 
            c.CreatedDate, 
            c.IsActive 
        };
    });
}
```

Try it: [EF Core](https://dotnetfiddle.net/zGSrJR) | [EF6](https://dotnetfiddle.net/KxWXDR)

 - It will update all the records except for the last record.
 - The `ModifiedDate` property is only updated for the first two records and not for the last record.
