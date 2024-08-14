---
Title: How EFE Bulk Extensions Handle Explicit Values in EF Core
MetaDescription: How EFE Bulk Extensions Handle Explicit Values in EF Core
LastMod: 2024-08-14
---

# How EFE Bulk Extensions Handle Explicit Values in EF Core

Before diving into this topic, make sure you've read the article [How Does EF Core Handle Explicit Values for Generated Properties?](https://www.learnentityframeworkcore.com/model/generated-value). We will build upon the concepts introduced there to explore how EFE Bulk Extensions manage explicit values in EF Core.

## What is the Default Behavior for BulkSaveChanges?

We have discussed the default behavior of `SaveChanges` in this section of our related article: [How EF Core Handles Generated Values for SaveChanges](https://www.learnentityframeworkcore.com/model/generated-value#the-truth-about-how-ef-core-handles-generated-value-properties).

The [BulkSaveChanges](https://entityframework-extensions.net/bulk-savechanges) method has almost identical behavior to `SaveChanges`, with one significant difference:

| Configuration Type | Behavior on Insert | Behavior on Update |
| :---------- | :---------- | :---------- |
| Identity  | Ignore | Ignore |

Unlike `SaveChanges`, which has a `Save` state, `BulkSaveChanges` uses the `Ignore` state for identity columns on both insert and update.

It is still possible to insert explicit identity values using the [InsertKeepIdentity](https://entityframework-extensions.net/identity) option:

```csharp
context.BulkSaveChanges(options => { options.InsertKeepIdentity = true; });
```

However, updating identity values is not supported, and generally, such a scenario doesn’t make practical sense.

For a complete comparison, see this [Online Example](https://dotnetfiddle.net/P36tk0).

## What is the Default Behavior for Other Bulk Extension Methods?

The default behavior for other bulk extension methods such as [BulkInsert](https://entityframework-extensions.net/bulk-insert), [BulkInsertOptimized](https://entityframework-extensions.net/bulk-insert-optimized), [BulkUpdate](https://entityframework-extensions.net/bulk-update), [BulkMerge](https://entityframework-extensions.net/bulk-merge), and [BulkSynchronize](https://entityframework-extensions.net/bulk-synchronize) is slightly more complex.

Here are the three differences in default behavior for our bulk extensions compared to `SaveChanges`, as demonstrated in this [Online Example](https://dotnetfiddle.net/ZC456P):

| Configuration Type | Behavior on Insert | Behavior on Update |
| :---------- | :---------- | :---------- |
| Default Value | Ignore | Save |
| Identity  | Ignore | Ignore |
| ValueGeneratedOnAdd | Ignore | Save |

While the behavior for `Identity` mirrors what we observed in the `BulkSaveChanges` method, new behaviors for `Default Value` and `ValueGeneratedOnAdd` have been introduced.

## ExplicitValueResolutionMode 
To increase flexibility, starting from [version 8.103.09.0](https://github.com/zzzprojects/EntityFramework-Extensions/releases/tag/8.103.0.0), we added the `ExplicitValueResolutionMode` options. You can modify the default behavior by selecting one of these six states:

- **Ignore:** The default value ([Online Example](https://dotnetfiddle.net/upc1SI))
- **AlwaysKeepValueOnInsert:** Properties with generated values always retain the entity values on insert, even if no explicit value is specified ([Online Example](https://dotnetfiddle.net/b7qIDD))
- **AlwaysKeepValueOnUpdate:** Properties with generated values always retain the entity values on update, even if the value is the same as the original one ([Online Example](https://dotnetfiddle.net/uUh2Aj))
- **AlwaysKeepValueOnInsertOrUpdate:** Combines the behaviors of `AlwaysKeepValueOnInsert` and `AlwaysKeepValueOnUpdate` ([Online Example](https://dotnetfiddle.net/hf7ctT))
- **SmartDefaultValueOnBulkInsert:** This state allows BulkInsert to logically split an insert into multiple statements to behave like `SaveChanges` with default values ([Online Example](https://dotnetfiddle.net/wT6XLk))
- **SmartPartialResolutionOnBulkInsert:** Similar to `SmartDefaultValueOnBulkInsert` but doesn't insert for the `ValueGeneratedOnAddOrUpdate` ([Online Example](https://dotnetfiddle.net/mmNmkw))

For the `AlwaysKeepValueOnInsert`, `AlwaysKeepValueOnUpdate`, and `AlwaysKeepValueOnInsertOrUpdate` states, they force all our bulk extensions to always takes values from the entity regardless of whether an explicit value is specified.

For the `SmartDefaultValueOnBulkInsert` and `SmartPartialResolutionOnBulkInsert` states, applicable only to [BulkInsert](https://entityframework-extensions.net/bulk-insert) and [BulkInsertOptimized](https://entityframework-extensions.net/bulk-insert-optimized), these states enable our library to split a single insert into multiple insert statements. For example, if you have two entities, one with an explicit value and another without, and call the `BulkInsert` method, two insert statements will be created:

- One `BulkInsert` statement for entities without explicit values.
- Another `BulkInsert` statement for entities with explicit values.

Thus, if you have three properties that allow default values, a `BulkInsert` could create up to eight insert statements to cover all possible scenarios.

## Conclusion

In this article, we have observed that the [BulkSaveChanges](https://entityframework-extensions.net/bulk-savechanges) method behaves similarly to [SaveChanges](https://www.learnentityframeworkcore.com/model/generated-value#the-truth-about-how-ef-core-handles-generated-value-properties), except for the handling of the identity property.

We also discovered that other [Bulk Extensions](https://entityframework-extensions.net/bulk-extensions) methods have very similar behaviors, except for properties configured as `Identity`, `Default Value`, and `ValueGeneratedOnAdd`.

Moreover, we explored how you can modify the default behavior of these extensions by specifying the `ExplicitValueResolutionMode` state.

One of the most crucial insights from this article is the introduction of the `SmartDefaultValueOnBulkInsert` and `SmartPartialResolutionOnBulkInsert` states. These options now allow `BulkInsert` operations to be split into multiple distinct insert statements, ensuring that explicit values are inserted correctly when specified.