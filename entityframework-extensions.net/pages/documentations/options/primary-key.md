---
Title: Primary Key Options with Entity Framework Extensions
MetaDescription: Learn how to use ColumnPrimaryKeyExpression, ColumnPrimaryKeyNames, and PrimaryKeyAndFormula in Entity Framework Extensions to handle custom keys in bulk operations. Perfect for scenarios like external keys, different identifiers, and conditional updates.
LastMod: 2025-08-11
---

# Primary Key Options with Entity Framework Extensions

The `ColumnPrimaryKeyExpression` and `ColumnPrimaryKeyNames` options allow you to perform bulk operations with a **custom key**.
Instead of using the primary key mapped in your entities, these options use the key you provide.

A custom key is often required in these two common scenarios:

* **External Key**: You need to save data but only know the external key from another system.
* **Different Key**: You need to save data but only know the customer `code` instead of the `id`.

These situations often occur when importing data.

---

## Using `ColumnPrimaryKeyExpression`

You can use `ColumnPrimaryKeyExpression` to specify the property (or properties) that will be used as the key:

* **Simple lambda expression** — can only be used when mapping **one property**
* **Lambda with a body** — can be used to map **one or more properties**

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

// Single property (simple lambda)
context.BulkUpdate(list, options =>
{
    options.ColumnPrimaryKeyExpression = x => x.ID;
});

// Multiple properties (lambda with a body)
context.BulkUpdate(list, options =>
{
    options.ColumnPrimaryKeyExpression = x => new { x.ID, x.ID2 };
});
```

---

## Using `ColumnPrimaryKeyNames`

You can use `ColumnPrimaryKeyNames` to specify the key properties by providing a list of strings:

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

// Single property from an external list (dynamic source)
var externalKeyList = new List<string> { "ID" };
context.BulkUpdate(list, options =>
{
    options.ColumnPrimaryKeyNames = externalKeyList;
});

// Multiple properties using nameof()
context.BulkUpdate(list, options =>
{
    options.ColumnPrimaryKeyNames = new List<string>
    {
        nameof(MyEntity.ID),
        nameof(MyEntity.ID2)
    };
});
```

---

You can learn more about options configuration and the difference between the `Expression` and `Names` suffix in these articles:

* [Configure Options](https://entityframework-extensions.net/configure-options)
* [Configure Column Options](https://entityframework-extensions.net/configure-column-options)

---

## When Your EF Core Primary Key Isn’t Enough — How to Handle Custom Keys

An entity to be saved in EF Core always requires a **key**.

There are multiple ways to map the key, such as:

* [Convention-based configuration](https://learn.microsoft.com/en-us/ef/core/modeling/keys?tabs=data-annotations#configuring-a-primary-key) (naming a property `ID` or `<EntityName>ID`)
* [Mapping a key with Data Annotations](https://www.learnentityframeworkcore.com/configuration/data-annotation-attributes/key-attribute)
* [Mapping a key with Fluent API](https://www.learnentityframeworkcore.com/configuration/fluent-api/haskey-method)

A key can be:

* A **single key** (one property)
* A **composite key** (multiple properties)

Everything works fine until you hit one of these two common scenarios:

* **External Key**: You need to save data but only know the external key from another system.
* **Different Key**: You need to save data but only know the customer `code` instead of the `id`.

---

### So what’s the solution when this happens?

* [WhereBulkContains](https://entityframework-extensions.net/where-bulk-contains) — Allows you to retrieve entities using a custom key, and then copy properties to those retrieved entities.
* [SyncPropertiesFromDatabaseValues](https://entityframework-plus.net/ef-core-sync-properties-from-database-values) — Allows you to synchronize the key property from a custom key, then attach those entities and save them.

Both solutions work fine — but there’s an even better one: **using a custom key directly!**

You can do this with any of our [bulk operations](https://entityframework-extensions.net/bulk-extensions) by using the `ColumnPrimaryKeyExpression` or `ColumnPrimaryKeyNames` options.

## How to Customize the Primary Key Even More

Sometimes you need to add extra logic to the primary key to make it even more specific — for example, ensuring you do **not** update rows that are “locked.”

For these special scenarios, we offer four additional methods:

* **InsertPrimaryKeyAndFormula** — Allows you to customize the logic after the primary key for the [BulkInsert](https://entityframework-extensions.net/bulk-insert) method. Requires the use of the `InsertIfNotExists` option.
* **UpdatePrimaryKeyAndFormula** — Allows you to customize the logic after the primary key for the [BulkUpdate](https://entityframework-extensions.net/bulk-update) method.
* **DeletePrimaryKeyAndFormula** — Allows you to customize the logic after the primary key for the [BulkDelete](https://entityframework-extensions.net/bulk-delete) method.
* **MergePrimaryKeyAndFormula** — Allows you to customize the logic after the primary key for the [BulkMerge](https://entityframework-extensions.net/bulk-merge) method.

⚠️ **Be careful with `BulkMerge`** — if entities don’t match the condition, they will be inserted instead.
In most cases, you’ll want to use the `MergeMatchedAndFormula` option instead, which skips the update for matched entities if the condition is not satisfied.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

// Use a formula to avoid updating "locked" rows
context.BulkUpdate(list, options =>
{
    // Note: UpdateMatchedAndFormula would be a better choice for this scenario
    options.UpdatePrimaryKeyAndFormula = "NOT DestinationTable.IsLocked";
});
```

## Choosing Between Expression, Names, and PrimaryKeyAndFormula

| Option                           | Best For                                                    | Pros                                                                        | Cons                                                              |
| -------------------------------- | ----------------------------------------------------------- | --------------------------------------------------------------------------- | ----------------------------------------------------------------- |
| **`ColumnPrimaryKeyExpression`** | When you know the property names at compile time            | Type-safe (compile-time checking), easier refactoring, IntelliSense support | Requires recompilation if the key changes                         |
| **`ColumnPrimaryKeyNames`**      | When the key properties come from a dynamic source          | Flexible for runtime/dynamic scenarios                                      | No compile-time checking, risk of typos in property names         |
| **`PrimaryKeyAndFormula`**       | When you need to add extra conditions to the matching logic | Handles advanced scenarios (e.g., skip “locked” rows)                       | More complex, requires SQL syntax, potential performance overhead |


## Conclusion

Handling primary keys in EF Core is straightforward — until you face real-world scenarios like importing data from another system or working with a different unique identifier.
With **`ColumnPrimaryKeyExpression`** and **`ColumnPrimaryKeyNames`**, you can easily define a custom key for your bulk operations, avoiding the need for extra lookups or complex workarounds.

And when you need even more control, the **PrimaryKeyAndFormula** options let you add custom matching logic, giving you the flexibility to handle edge cases like “locked” rows or conditional updates.

By choosing the right approach for your scenario, you can keep your bulk operations **fast**, **accurate**, and **tailored** to your data requirements.

---

## 📚 Related Articles

### Column Options
- [Input / Output / Ignore](/input-output-ignore)
- [Primary Key](/primary-key)

### Coalesce Options
- [Coalesce](/coalesce)
- [Coalesce Destination](/coalesce-destination)

### Matched Options
- [Matched and Condition](/matched-and-condition)
- [Matched and One NOT Condition](/matched-and-one-not-condition)
- [Matched and Formula](/matched-and-formula)

### Delete Matched Options
- [Delete Matched and Condition](/delete-matched-and-condition)
- [Delete Matched and One NOT Condition](/delete-matched-and-one-not-condition)
- [Delete Matched and Formula](/delete-matched-and-formula)