---
Name: PreBulkSaveChanges
LastMod: 2025-06-23
---

# PreBulkSaveChanges

## Description

The `PreBulkSaveChanges` event is raised as soon as the `BulkSaveChanges` method is called and no configuration or anything else is done before. It allows you to set some global configuration for `BulkSaveChanges`. 

## Example

The following example sets the `CreatedDate` for new customers and `ModifiedDate` property for existing customers.

```csharp
EntityFrameworkManager.PreBulkSaveChanges = ctx =>
{
    var modifiedEntities = ctx.ChangeTracker.Entries().ToList();

    foreach (var change in modifiedEntities)
    {
        if (change.State == EntityState.Added)
        {
            change.CurrentValues["CreatedDate"] = DateTime.Now;
        }
        else if (change.State == EntityState.Modified)
        {
            change.CurrentValues["ModifiedDate"] = DateTime.Now;
        }
    }
};
```

[Try it in EF Core](https://dotnetfiddle.net/5Zj4gQ) | [Try it in EF6](https://dotnetfiddle.net/3nTyrH)

In the `PreBulkSaveChanges` event, the `CreatedDate` for new customers and the `ModifiedDate` property for existing customers are set to `DateTime.Now` before the data is saved into the database.
