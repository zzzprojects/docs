---
Name: PreBulkDelete
LastMod: 2025-06-23
---

# PreBulkDelete

## Description

The `PreBulkDelete` event is raised as soon as the `BulkDelete` method is called and no configuration or anything else is done before. It allows you to set some global configuration for `BulkDelete`. 

## Example

The following example softly deletes the list of customers by updating the `IsDeleted` to `true` and clearing the list.

```csharp
EntityFrameworkManager.PreBulkDelete = (ctx, obj) => 
{
    var list = obj as IEnumerable<Customer>;

    foreach (var customer in list)
    {
        customer.IsDeleted = true;
    }

    ctx.BulkUpdate(list);

    ((List<Customer>)obj).Clear();
};
```

[Try it in EF Core](https://dotnetfiddle.net/S1X9u1) | [Try it in EF6](https://dotnetfiddle.net/9ExoFg)

 - In the `PreBulkDelete` event, the `IsDeleted` is set to `true` and the `obj` list is cleared (list of customers).
 - So that `BulkDelete` doesn't delete the data from the database permanently.
