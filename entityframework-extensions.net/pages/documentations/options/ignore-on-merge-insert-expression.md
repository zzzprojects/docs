---
Name: Ignore OnMergeInsert Expression
LastMod: 2025-06-24
---

# Ignore OnMergeInsert Expression

## Description

The `IgnoreOnMergeInsertExpression` allows you to ignore some columns when the `BulkMerge` method executes the `insert` statement and these columns will only be used in `update` statement.

The following example ignores the `Description` property during insertion, but it will be considered when updating the records.

```csharp
using (var context = new EntityContext())
{
    context.BulkMerge(list, options => 
    {
        options.IgnoreOnMergeInsertExpression = customer => new 
        {
            customer.CustomerID,  
            customer.Description
        };
    });
}
```
Try it: [EF Core](https://dotnetfiddle.net/SFCCCZ) | [EF6](https://dotnetfiddle.net/ggtMXb)
