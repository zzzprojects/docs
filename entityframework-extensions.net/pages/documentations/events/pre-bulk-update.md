---
Name: PreBulkUpdate
LastMod: 2025-06-23
---

# PreBulkUpdate

## Description

The `PreBulkUpdate` event is raised as soon as the `BulkUpdate` method is called and no configuration or anything else is done before. It allows you to set some global configuration for BulkUpdate. 

## Example

The following example sets the `ModifiedDate` property of a `Customer`.

```csharp
EntityFrameworkManager.PreBulkUpdate = (ctx, obj) => 
{
    if(obj is IEnumerable<Customer>) 
    {
        foreach(var customer in (IEnumerable<Customer>)obj)
        {
            customer.ModifiedDate = DateTime.Now;
        }
    }
};
```

[Try it in EF Core](https://dotnetfiddle.net/TCvCOm) | [Try it in EF6](https://dotnetfiddle.net/9JUluL)

In the `PreBulkUpdate` event, the `ModifiedDate` property is set to `DateTime.Now` before the data is updated in the database.
