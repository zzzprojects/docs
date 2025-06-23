---
Name: PreBulkMerge
LastMod: 2025-06-23
---

# PreBulkMerge

## Description

The `PreBulkMerge` event is raised as soon as the `BulkMerge` method is called and no configuration or anything else is done before. It allows you to set some global configuration for `BulkMerge`. 

## Example

The following example sets the `CreatedDate` for new customers and `ModifiedDate` property for existing customers.

```csharp
EntityFrameworkManager.PreBulkMerge = (ctx, obj) =>
{
    if (obj is IEnumerable<Customer>)
    {
        foreach (var customer in (IEnumerable<Customer>)obj)
        {
            if (customer.CustomerID == 0)
            {
                customer.CreatedDate = DateTime.Now;
            }
            else
            {
                customer.ModifiedDate = DateTime.Now;
            }
        }
    }
};
```

[Try it in EF Core](https://dotnetfiddle.net/SK2Prc) | [Try it in EF6](https://dotnetfiddle.net/YsLVnQ)

In the `PreBulkMerge` event, the `CreatedDate` for new customers and the `ModifiedDate` property for existing customers are set to `DateTime.Now` before the data is merged into the database.
