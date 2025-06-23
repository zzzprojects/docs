---
Name: PreBulkSynchronize
LastMod: 2025-06-23
---

# PreBulkSynchronize

## Description

The `PreBulkSynchronize` event is raised as soon as the `BulkSynchronize` method is called and no configuration or anything else is done before. It allows you to set some global configuration for `BulkSynchronize`. 

## Example

The following example sets the `CreatedDate` for new customers and `ModifiedDate` property for existing customers.

```csharp
EntityFrameworkManager.PreBulkSynchronize = (ctx, obj) =>
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

[Try it in EF Core](https://dotnetfiddle.net/GTBAYt) | [Try it in EF6](https://dotnetfiddle.net/EbuRR2)

In the `PreBulkSynchronize` event, the `CreatedDate` for new customers and the `ModifiedDate` property for existing customers are set to `DateTime.Now` before the data is synchronized into the database.
