---
Title: Events in Entity Framework Extensions
MetaDescription: Learn how to use events in Entity Framework Extensions to customize bulk operations. Run custom logic before or after inserts, updates, deletes, merges, and synchronizations—perfect for setting audit fields like CreatedDate and ModifiedDate, applying soft deletes, and logging.
LastMod: 2025-08-20
---

# ⚡ Events Options in EF Extensions

When working with [bulk operations](/bulk-extensions) in Entity Framework Extensions, you often need to apply **custom business logic** instead of just saving data as-is.

With **events**, you can intercept the process and decide what should happen **before or after** the operation.

The most common scenario is setting audit fields such as `CreatedDate` and `ModifiedDate` automatically.

But events are not limited to auditing — you can also:

* **Validate data** before processing
* **Modify column mappings** dynamically
* **Change the destination table name** at runtime
* **Log or clean up data after processing**

Think of events as **lifecycle hooks** for your bulk methods. They keep your logic centralized, reusable, and consistent without duplicating code in multiple places.

---

## 🏷️ Types of Events

Entity Framework Extensions provides multiple events that let you **customize bulk operations at different stages**.

You can think of them as checkpoints during the lifecycle of a bulk method:

* **Pre events** run before the operation is executed. They’re ideal for tasks like setting audit fields, validating data, or even swapping the destination table name.
* **Post events** run after the operation is finished. They’re useful for cleanup, logging, adjusting in-memory entities, or chaining additional logic.

Here are the available events:

| Event Pair                                         | Trigger Time                              | Common Use Case                                            |
| -------------------------------------------------- | ----------------------------------------- | ---------------------------------------------------------- |
| **PreBulkInsert / PostBulkInsert**                 | Before / After `BulkInsert`               | Set `CreatedDate` / Log inserted rows                      |
| **PreBulkUpdate / PostBulkUpdate**                 | Before / After `BulkUpdate`               | Set `ModifiedDate` / Trigger refresh, logging              |
| **PreBulkDelete / PostBulkDelete**                 | Before / After `BulkDelete`               | Implement soft deletes / Audit or cleanup  
| **PreBulkMerge / PostBulkMerge**                   | Before / After `BulkMerge`                | Set `CreatedDate` & `ModifiedDate` / Track merged entities |                |
| **PreBulkSynchronize / PostBulkSynchronize**       | Before / After `BulkSynchronize`          | Manage audit fields / Monitor sync results                 |
| **PreBulkSaveChanges / PostBulkSaveChanges**       | Before / After `BulkSaveChanges`          | Apply rules via ChangeTracker / Log save outcome           |
| **PreBatchSaveChanges / PostBatchSaveChanges**     | Before / After `BatchSaveChanges`         | Global batch rules / Batch completion tasks                |
| **PostConfiguration**                              | Right before bulk method executes         | Last-minute config like logging or mappings                |
| **BulkOperationExecuting / BulkOperationExecuted** | Before / After an operation inside a call | Fine-grained control, adjust values / log results          |

⚠️ **Warning:** When using the [IncludeGraph](/include-graph) feature, the events `PreBulkInsert`, `PreBulkUpdate`, `PreBulkMerge`, `PreBulkDelete`, `PreBulkSynchronize` — and their corresponding `Post` events — are triggered **only for root entities**. They are **not fired for the related entities** included in the graph.


---

## 🏷️ PreBulkInsert and PostBulkInsert

* **PreBulkInsert** runs before [BulkInsert](/bulk-insert) or [BulkInsertOptimized](/bulk-insert-optimized). Commonly used to set `CreatedDate` or other default values.
* **PostBulkInsert** runs after [BulkInsert](/bulk-insert) or [BulkInsertOptimized](/bulk-insert-optimized). Useful for logging results or adjusting in-memory entities once the data is saved.

The following example uses the `PreBulkInsert` event to set the `CreatedDate` property of each `Customer`:

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

EntityFrameworkManager.PreBulkInsert = (ctx, obj) =>
{
    if (obj is IEnumerable<Customer> list) 
    {
        foreach (var customer in list)
            customer.CreatedDate = DateTime.Now;
    }
};
```

[Online Example (EF Core)](https://dotnetfiddle.net/sdkwxW) | [Online Example (EF6)](https://dotnetfiddle.net/9JUluL)

⚠️ **Warning:** When using [IncludeGraph](/include-graph), `PreBulkInsert` and `PostBulkInsert` are triggered **only for root entities** — not for related entities included in the graph.

---

## 🏷️ PreBulkUpdate and PostBulkUpdate

* **PreBulkUpdate** runs before [BulkUpdate](/bulk-update). Often used to set `ModifiedDate`, validate data consistency, or enforce version numbers.
* **PostBulkUpdate** runs after [BulkUpdate](/bulk-update). Useful for triggering domain events, clearing caches, or logging changes.

The following example uses the `PreBulkUpdate` event to set the `ModifiedDate` property of each `Customer`:

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

EntityFrameworkManager.PreBulkUpdate = (ctx, obj) =>
{
    if (obj is IEnumerable<Customer> list) 
    {
        foreach (var customer in list)
            customer.ModifiedDate = DateTime.Now;
    }
};
```

[Online Example (EF Core)](https://dotnetfiddle.net/TCvCOm) | [Online Example (EF6)](https://dotnetfiddle.net/9JUluL)

⚠️ **Warning:** When using [IncludeGraph](/include-graph), `PreBulkUpdate` and `PostBulkUpdate` are triggered **only for root entities** — not for related entities included in the graph.

---

## 🏷️ PreBulkDelete and PostBulkDelete

* **PreBulkDelete** runs before [BulkDelete](/bulk-delete). Often used for **soft deletes**, such as setting an `IsDeleted` flag instead of removing rows.
* **PostBulkDelete** runs after [BulkDelete](/bulk-delete). Useful for logging, auditing, or archiving deletion results.

The following example uses the `PreBulkDelete` event to mark each `Customer` as deleted (`IsDeleted = true`). The list is then cleared, so the `BulkDelete` method will not physically remove rows — it will only update them:

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

EntityFrameworkManager.PreBulkDelete = (ctx, obj) => 
{
    if (obj is IEnumerable<Customer> list)
    {
        foreach (var customer in list)
            customer.IsDeleted = true;

        ctx.BulkUpdate(list);
        ((List<Customer>)obj).Clear(); // Prevents physical delete
    }
};
```

[Online Example (EF Core)](https://dotnetfiddle.net/S1X9u1) | [Online Example (EF6)](https://dotnetfiddle.net/9ExoFg)

⚠️ **Warning:** When using [IncludeGraph](/include-graph), `PreBulkDelete` and `PostBulkDelete` are triggered **only for root entities** — not for related entities included in the graph.

---

## 🏷️ PreBulkMerge and PostBulkMerge

* **PreBulkMerge** runs before [BulkMerge](/bulk-merge). Useful for setting both `CreatedDate` and `ModifiedDate`, depending on whether the entity is new or existing.
* **PostBulkMerge** runs after [BulkMerge](/bulk-merge). Often used to track which entities were merged or to log synchronization results.

The following example uses the `PreBulkMerge` event to set the `CreatedDate` for new customers and the `ModifiedDate` for existing ones:

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

EntityFrameworkManager.PreBulkMerge = (ctx, obj) =>
{
    if (obj is IEnumerable<Customer> list)
    {
        foreach (var customer in list)
        {
            if (customer.CustomerID == 0)
                customer.CreatedDate = DateTime.Now;
            else
                customer.ModifiedDate = DateTime.Now;
        }
    }
};
```

[Online Example (EF Core)](https://dotnetfiddle.net/SK2Prc) | [Online Example (EF6)](https://dotnetfiddle.net/YsLVnQ)

⚠️ **Warning:** When using [IncludeGraph](/include-graph), `PreBulkMerge` and `PostBulkMerge` are triggered **only for root entities** — not for related entities included in the graph.

---

## 🏷️ PreBulkSynchronize and PostBulkSynchronize

* **PreBulkSynchronize** runs before [BulkSynchronize](/bulk-synchronize). Commonly used to set audit fields when syncing external data.
* **PostBulkSynchronize** runs after [BulkSynchronize](/bulk-synchronize). Useful for reporting, monitoring, or verifying synchronization results.

The following example uses the `PreBulkSynchronize` event to set the `CreatedDate` for new customers and the `ModifiedDate` for existing ones:

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

EntityFrameworkManager.PreBulkSynchronize = (ctx, obj) =>
{
    if (obj is IEnumerable<Customer> list)
    {
        foreach (var customer in list)
        {
            if (customer.CustomerID == 0)
                customer.CreatedDate = DateTime.Now;
            else
                customer.ModifiedDate = DateTime.Now;
        }
    }
};
```

[Online Example (EF Core)](https://dotnetfiddle.net/GTBAYt) | [Online Example (EF6)](https://dotnetfiddle.net/EbuRR2)

⚠️ **Warning:** When using [IncludeGraph](/include-graph), `PreBulkSynchronize` and `PostBulkSynchronize` are triggered **only for root entities** — not for related entities included in the graph.


---

## 🏷️ PreBulkSaveChanges and PostBulkSaveChanges

* **PreBulkSaveChanges** runs before [BulkSaveChanges](/bulk-savechanges). Useful for applying rules across all tracked entities.
* **PostBulkSaveChanges** runs after [BulkSaveChanges](/bulk-savechanges). Often used for logging or refreshing the in-memory state.

The following example uses the `PreBulkSaveChanges` event to set the `CreatedDate` for new customers and the `ModifiedDate` for existing customers:

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

EntityFrameworkManager.PreBulkSaveChanges = ctx =>
{
    foreach (var change in ctx.ChangeTracker.Entries().ToList())
    {
        if (change.State == EntityState.Added)
            change.CurrentValues["CreatedDate"] = DateTime.Now;
        else if (change.State == EntityState.Modified)
            change.CurrentValues["ModifiedDate"] = DateTime.Now;
    }
};
```

[Online Example (EF Core)](https://dotnetfiddle.net/5Zj4gQ) | [Online Example (EF6)](https://dotnetfiddle.net/3nTyrH)


---

## 🏷️ PreBatchSaveChanges and PostBatchSaveChanges

* **PreBatchSaveChanges** runs before [BatchSaveChanges](/batch-savechanges). Useful for enforcing global rules across batch operations.
* **PostBatchSaveChanges** runs after [BatchSaveChanges](/batch-savechanges). Often used for logging or handling batch completion tasks.

The following example uses the `PreBatchSaveChanges` event to set the `CreatedDate` for new customers and the `ModifiedDate` for existing customers:

```csharp
// @nuget: Z.EntityFramework.Extensions
using Z.EntityFramework.Extensions;

EntityFrameworkManager.PreBatchSaveChanges = ctx =>
{
    foreach (var change in ctx.ChangeTracker.Entries().ToList())
    {
        if (change.State == EntityState.Added)
            change.CurrentValues["CreatedDate"] = DateTime.Now;
        else if (change.State == EntityState.Modified)
            change.CurrentValues["ModifiedDate"] = DateTime.Now;
    }
};
```

[Online Example (EF6)](https://dotnetfiddle.net/UtcHa2)

⚠️ **Warning:** `BatchSaveChanges` is available **only in EF6**. It is **not supported in EF Core**.

---

## 🏷️ PostConfiguration

The `PostConfiguration` event runs **right before a bulk method executes**, after all options have been configured.

It’s a great place to apply **last-minute adjustments**, such as enabling logging, applying filters, or tuning options dynamically.

The following example uses the `PostConfiguration` event to set a custom `FormulaInsert` on the `Description` column:

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

context.BulkInsert(list, options =>
{
    options.PostConfiguration = ops =>
    {
        ops.ColumnMappings
           .Find(x => x.SourceName == "Description")
           .FormulaInsert = "'any valid sql statement'";
    };
});
```

[Online Example (EF Core)](https://dotnetfiddle.net/5fPyFY) | [Online Example (EF6)](https://dotnetfiddle.net/8q6BdX)

⚠️ **Warning:** There is **no `PreConfiguration` event**. Configuration can only be adjusted at the `PostConfiguration` stage.

---

## 🏷️ BulkOperationExecuting and BulkOperationExecuted

* **BulkOperationExecuting** runs **before an individual bulk operation** inside a call. Great for tweaking values or applying validations.
* **BulkOperationExecuted** runs **after an individual bulk operation**. Useful for marking entities, updating in-memory state, or logging results.

The following example demonstrates how to use both events:

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

var list = new List<Customer>() 
{ 
	new Customer()	{ Name ="Customer_A", IsActive = true},
	new Customer()	{ Name ="Customer_B", IsActive = true},
	new Customer()	{ Name ="Customer_C", IsActive = true}
};
context.Customers.AddRange(list);

context.BulkSaveChanges(options =>
{
    options.BulkOperationExecuting = op =>
    {
        list.ForEach(x => 
        { 
            x.Description = "Before Execution";
            x.IsActive = false;
        });
    };

    options.BulkOperationExecuted = op =>
    {
        list.ForEach(x => 
        { 
            x.Description = "After Execution";
            x.IsActive = true;
        });
    };
});
```

[Online Example (EF Core)](https://dotnetfiddle.net/TEE4xQ) | [Online Example (EF6)](https://dotnetfiddle.net/mIhWyT)

⚠️ **Warning:** These events run **per bulk operation**, not per entity. If a call executes multiple operations (for example, one insert and one update batch), both `BulkOperationExecuting` and `BulkOperationExecuted` will fire for each operation.

---

## 🔎 Comparison at a Glance

| Event Pair                                         | Trigger Time                              | Common Use Case                                            |
| -------------------------------------------------- | ----------------------------------------- | ---------------------------------------------------------- |
| **PreBulkInsert / PostBulkInsert**                 | Before / After `BulkInsert`               | Set `CreatedDate` / Log inserted rows                      |
| **PreBulkUpdate / PostBulkUpdate**                 | Before / After `BulkUpdate`               | Set `ModifiedDate` / Trigger refresh, logging              |
| **PreBulkDelete / PostBulkDelete**                 | Before / After `BulkDelete`               | Soft deletes / Audit deletions   						  |
| **PreBulkMerge / PostBulkMerge**                   | Before / After `BulkMerge`                | Set `CreatedDate` & `ModifiedDate` / Track merged entities |             
| **PreBulkSynchronize / PostBulkSynchronize**       | Before / After `BulkSynchronize`          | Audit fields for sync / Monitor results                    |
| **PreBulkSaveChanges / PostBulkSaveChanges**       | Before / After `BulkSaveChanges`          | Apply rules via ChangeTracker / Log save outcome           |
| **PreBatchSaveChanges / PostBatchSaveChanges**     | Before / After `BatchSaveChanges`         | Global batch rules / Batch completion tasks                |
| **PostConfiguration**                              | Right before bulk method executes         | Configure logging or options                               |
| **BulkOperationExecuting / BulkOperationExecuted** | Before / After an operation inside a call | Adjust entity values / Log results                         |

---

## 📌 When to Use

Events are useful when you want to:

* Automatically set `CreatedDate` and `ModifiedDate`.
* Apply **soft delete rules** instead of physical deletes.
* Log database commands and SQL queries for debugging or auditing.
* Enforce validation or business rules globally before any data hits the database.
* Run cleanup or reporting after bulk operations finish.

---

## ⭐ Why It’s Useful

With events you can:

* Automate audit field management without repeating code.
* Ensure consistent rules across bulk operations.
* Centralize logic in one place, reducing duplication and errors.
* Add flexible, custom behaviors before **and** after the operation.

---

## 📚 Related Articles

* [Configure Options](/configure-options)
* [Bulk Extensions](/bulk-extensions)

---

## 🏁 Conclusion

Events in Entity Framework Extensions give you powerful hooks into the lifecycle of bulk operations.

* Use **Pre events** to adjust or validate data before saving.
* Use **Post events** to log, monitor, or adjust state after saving.
* Use **operation-specific events** when you need fine-grained control inside a single call.

🎯 The result: cleaner code, consistent rules, and fewer surprises in your database.
