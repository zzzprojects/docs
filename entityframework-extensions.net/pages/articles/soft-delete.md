---
Name: Soft Delete
LastMod: 2025-06-22
---

# Soft Delete

## Problem
You want to soft delete some entities currently in a deleted state with BulkSaveChanges.


```csharp
using (var ctx = new CurrentContext())
{
    var lastLogin = DateTime.Now.AddYears(-2);
    var list = ctx.Customers.Where(x => x.LastLogin < lastLogin).ToList();
    ctx.Customers.RemoveRange(list);
    
    // HOW to automatically handle soft delete?
    ctx.BulkSaveChanges();
}
```

### Solution
You can use the **EntityFrameworkManager.PreBulkSaveChanges** property to change state & value from entities prior to the BulkSaveChanges execution.

### Example


```csharp
EntityFrameworkManager.PreBulkSaveChanges = context =>
{
    foreach (var entry in context.ChangeTracker.Entries())
    {
        if (entry.State == EntityState.Deleted && entry.Entity is Customer)
        {
            entry.State = EntityState.Modified;
            ((Customer) entry.Entity).IsDeleted = true;
        }
    }
};

using (var ctx = new CurrentContext())
{
    var lastLogin = DateTime.Now.AddYears(-2);
    var list = ctx.Customers.Where(x => x.LastLogin < lastLogin).ToList();
    ctx.Customers.RemoveRange(list);
    ctx.BulkSaveChanges();
}
```

## Problem
You want to soft delete some entities currently in a deleted state with BulkDelete.


```csharp
using (var ctx = new CurrentContext())
{
    var lastLogin = DateTime.Now.AddYears(-2);
    var list = ctx.Customers.Where(x => x.LastLogin < lastLogin).ToList();
    
    // HOW to automatically handle soft delete?
    ctx.BulkDelete(list);
}
```

### Solution
Unfortunately, there is no way to SoftDelete using BulkDelete. Since BulkDelete doesn't use ChangeTracker for optimization reasons, you will have to implement yourself an extension method to handle this scenario.

### Example


```csharp
public static class Extensions
{
    public static void BulkHardOrSoftDelete<T>(this DbContext ctx, IEnumerable<T> items) where T : class
    {
        var hardDelete = new List<T>();
        var softDelete = new List<T>();

        foreach (var item in items)
        {
            var customer = item as Customer;
            if (customer != null)
            {
                customer.IsDeleted = true;
                softDelete.Add(item);
            }
            else
            {
                hardDelete.Add(item);
            }
        }

        if (hardDelete.Count > 0)
        {
            ctx.BulkDelete(hardDelete);
        }
        if (softDelete.Count > 0)
        {
            ctx.BulkUpdate(softDelete);
        }
    }
}

using (var ctx = new CurrentContext())
{
	var lastLogin = DateTime.Now.AddYears(-2);
	var list = ctx.Customers.Where(x => x.LastLogin < lastLogin).ToList();

	ctx.BulkHardOrSoftDelete(list);
}
```

