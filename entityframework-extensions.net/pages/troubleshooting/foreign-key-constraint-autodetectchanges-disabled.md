---
Name: Foreign Key Constraint Troubleshooting
LastMod: 2025-06-26
---

# Foreign Key Constraint

## Problem

You execute a method from the Entity Framework Extensions library, and the following error is thrown:

> The MERGE statement conflicted with the FOREIGN KEY constraint "[FK_Name]". The conflict occurred in database "[Database_Name]", table "[Table_Name]", column \'[Column_Name]\'.

And you use a code similar to this:


```csharp
using (var ctx = new MyEntities())
{
	ctx.Configuration.AutoDetectChangesEnabled = false;
	ctx.Categories.AddRange(categories);
	ctx.Items.AddRange(items);
	ctx.BulkSaveChanges();
}
```

### Cause

One cause could simply be a wrong save order provided by either Entity Framework or the EFE Library.

### Cause Source
The main reason that could cause this issue is disabling AutoDetectChanges and not enabling it before the SaveChanges/BulkSaveChanges.

When the AutoDetectChanges is disabled, there is no verification with related entities, and that could cause the item to be added before a new Category (Which should be added first)! Leading to the Foreign Key issue.

In addition, there is no reason why this code should disable DetectChanges. Since the AddRange method is used, the "DetectChanges" method is called only once per AddRange call, so you don't suffer from a performance issue.

### Solution

1. ADD the line AutoDetectChangesEnabled = true; before BulkSaveChanges
2. CALL ctx.ChangeTracker.DetectChanges();
3. REMOVE the line AutoDetectChangesEnabled = false;

