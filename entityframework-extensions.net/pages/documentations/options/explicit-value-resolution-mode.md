---
Title: How EFE Bulk Extensions handle explicit value in EF Core
MetaDescription: How EFE Bulk Extensions handle explicit value in EF Core
LastMod: 2024-06-20
---

**This article is currently in progress and will be fully completed by June 24**

# How EFE Bulk Extensions handle explicit value in EF Core?

When we initially added the support of Entity Framework Extensions to EF Core in 2016, we added it the same way as we did for EF6 as we were not aware yet that Generated Value property could have explicit value.

Thought EF Core versions, the support to explicit value has been dramatically improved but for our library, we never really did anything about it as we didn't want to cause any breaking changes.

As providing explicit value has becomming more coming, it also started to raise a lot of problems and questions from our customers since we were not supporting it.

In this article, we will learn how EFE Bulk Extensions handle explicit value in EF Core and how you can better control the explicit value behavior.

But first, make sure to read and understand the following article: [How to Handle Explicit Value for Generated Value Property in EF Core](https://www.learnentityframeworkcore.com/)

We will re-use multiple table and information so make sure you read it once or twice.

## What is the BulkSaveChanges default behavior?

The [BulkSaveChanges](https://entityframework-extensions.net/bulk-savechanges) method try to be as close as possible as the [EF Core SaveChanges](https://learn.microsoft.com/en-us/ef/core/saving/basic) method. So the default behavior when saving entities is almost the same beside one:

| Configuration Type | Behavior on Insert | Behavior on Update |
| :---------- | :---------- | :---------- |
| Default Value | Save | Save |
| Key  | Save | Throw |
| Identity  | **Ignore** | Throw |
| Concurrency | Save | Save |
| Computed | Ignore | Ignore |
| Timestamp |Ignore | Ignore |

The only different behavior is currently the identity that is ignored for the `BulkSaverChanges` method while inserted if an explicit value is provided in the `SaveChanges`. This behavior is due to some old logic that we made for EF6 to ensure we never insert by mistake in an identity column.

To force to insert in an identity column with `BulkSaveChanges`, you need to use the option [InsertKeepIdentity](https://entityframework-extensions.net/identity) such as:

```csharp
context.BulkSaveChanges(options => { options.InsertKeepIdentity = true; });
```

## What is other bulk operation default behavior?

The default behavior for other bulk operations ([BulkInsert](https://entityframework-extensions.net/bulk-insert), [BulkInsertOptimized](https://entityframework-extensions.net/bulk-insert-optimized), [BulkUpdate](https://entityframework-extensions.net/bulk-update), [BulkMerge](https://entityframework-extensions.net/bulk-merge), [BulkSynchronize](https://entityframework-extensions.net/bulk-synchronize)) is very simple: We always ignore ValueGenerated property no matter if an explicit value is provided or not.

But why do we always ignore it? Simply for performance reasons. Our Bulk Extensions has been created to be the fastest way to insert or update entities in your database, and every time we add some custom logic, we slow down the performance. So by default, we don't want to check every value generated property value for every entity to check if we should insert or update it and then split the bulk operations in multiple bulk operations depending of the result.

When we initially started to support EF Core in 2016, we didn't know that we could specify explicit value so, we didn't change this behavior for not doing any breaking changes. (Nah maybe not this)

## How to change bulk operations default behavior with ExplicitValueResolutionMode?

```csharp
namespace Z.EntityFramework.Extensions
{
    /// <summary>The explicit value resolution mode for a ValueGenerated property for all bulk operations (besides `BulkSaveChanges`). To learn more about this option, see: https://entityframework-extensions.net/explicit-value-resolution-mode</summary>
    public enum ExplicitValueResolutionMode
	{
        /// <summary>All explicit values are always ignored no matter the original configuration.</summary>
        Ignore,
        /// <summary>All explicit values are always inserted beside identity no matter the original configuration.</summary>
        AlwaysKeepValueOnInsert,
        /// <summary>All explicit values are always updated no matter the original configuration.</summary>
        AlwaysKeepValueOnUpdate,
        /// <summary>All explicit values are always inserted beside identity and always updated no matter the original configuration.</summary>
        AlwaysKeepValueOnInsertOrUpdate,
        /// <summary>Smart logic to insert explicit default values when provided, and ignore the property when not provided.</summary>
        SmartDefaultValueOnBulkInsert,
        /// <summary>Smart logic (still under developement) to insert explicit values when provided (support default value and a few more coming soon), and ignore the property when not provided.</summary>.
        SmartPartialResolutionOnBulkInsert
    }
}
```

Value:

- **Ignore:**
- **AlwaysKeepValueOnInsert:**
- **AlwaysKeepValueOnUpdate:**
- **AlwaysKeepValueOnInsertOrUpdate:**
- **SmartDefaultValueOnBulkInsert:**
- **SmartPartialResolutionOnBulkInsert:**

The both enum value starting with the **Smart** prefix means that a single bulk insert can be split into multiple bulk insert depending of the values. By example, if one entity have a value for a property with a default value and the other no, then they will be inserted into 2 differents insert. The first one will create an insert that will insert the value, the second one will create an insert without the column, so the default value will be inserted.

## Conclusion

While our [EF Core Bulk Extensions](https://entityframework-extensions.net/bulk-extensions) have different behavior for explicit value than `SaveChanges`, we have seen in this article that we can still control and be close to whatever we want.
