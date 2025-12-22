---
Name: Delete Matched and One NOT Condition Option in Entity Framework Extensions
MetaDescription: Learn how to use the 'DeleteMatchedAndOneNotCondition' option in Entity Framework Extensions to delete only when at least one value between source and destination is different. See examples, scenarios, and four configuration methods.
LastMod: 2025-08-17
---

# DeleteMatchedAndOneNotCondition Options in EF Extensions

The `DeleteMatchedAndOneNotCondition` option in Entity Framework Extensions lets you **perform the `DELETE` action only when at least one selected value in the source and destination is different**.  

If all selected values are equal, the delete is skipped. This is useful when you want to **protect certain records, enforce rules, or avoid removing entries that are still valid**.  

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

context.BulkDelete(customers, options => 
{
    // ON DELETE: only remove if "Version" is different between source and destination
    options.DeleteMatchedAndOneNotConditionExpression = x => new { x.Version };
});
````

Typical scenarios include:

* **Versioning (`Version`)** → Only delete if the record version differs, preventing accidental removal of up-to-date records.
* **Approval checks (`ApprovedBy`, `ApprovedDate`)** → Protect approved records from deletion unless one of these values changes.
* **Synchronization rules (`SyncKey`, `LastSyncDate`)** → Delete only when a mismatch indicates outdated or invalid records.
* **User profiles (`Email`, `Role`)** → Avoid deleting active users unless at least one critical field differs.

This option applies to the following methods in Entity Framework Extensions:

* [BulkDelete](/bulk-delete)

---

## 💡 Example Effect

| **ID** | **Destination.Version** | **Source.Version** | **Without Condition** | **With Condition** |
| ------ | ----------------------- | ------------------ | --------------------- | ------------------ |
| 1      | 1                       | 1                  | deleted             | skipped          |
| 2      | 3                       | 4                  | deleted             | deleted          |
| 3      | 5                       | 5                  | deleted             | skipped          |

---

## 🛠️ Prerequisites

Before continuing, we recommend reading these articles first to understand **how EF Extensions options work** and the **differences between column option types**:

* [Configure Options](/configure-options) – Learn the basics of setting and customizing options in EF Extensions.
* [Configure Column Options](/configure-column-options) – See how to configure column-specific behavior and when to use `Expression` (strongly typed, compile-time safe) vs. `Names` (string-based, dynamic at runtime).

---

## 📌 When to Use

Use the `DeleteMatchedAndOneNotCondition` option from Entity Framework Extensions when:

* You want to **delete only if at least one key field is different**
* You need to **protect records from being deleted if nothing meaningful has changed**
* You want to **enforce rules on deletions during synchronization or imports**

---

## ⭐ Why It’s Useful

Without `DeleteMatchedAndOneNotCondition`, using in EF Core a [BulkDelete](/bulk-delete) from Entity Framework Extensions could **remove rows unnecessarily**, even when the values are still valid.

Using this option, you can:

* Prevent unwanted deletions
* Protect important or approved records
* Keep synchronization logic simpler without custom SQL

---

## 🏢 Scenario

A company imports in EF Core customers using [BulkDelete](/bulk-delete) method from Entity Framework Extensions.

* Records should only be deleted if the `Version` field is different between source and destination.
* If versions match, the row should stay untouched.

In summary:

* If `Version` differs → delete the customer
* If `Version` is the same → skip deletion

---

### 🗝️ Solution

The `DeleteMatchedAndOneNotCondition` option offers **four ways** to configure behavior:

* **[DeleteMatchedAndOneNotConditionExpression](#deletematchedandonenotconditionexpression)** – include properties via a lambda expression
* **[DeleteMatchedAndOneNotConditionNames](#deletematchedandonenotconditionnames)** – include properties via a list of property names
* **[IgnoreOnDeleteMatchedAndOneNotConditionExpression](#ignoreondeletematchedandonenotconditionexpression)** – exclude properties via a lambda expression, all others properties are included.
* **[IgnoreOnDeleteMatchedAndOneNotConditionNames](#ignoreondeletematchedandonenotconditionnames)** – exclude properties via a list of property names, all others properties are included.

---

## 🏷️ DeleteMatchedAndOneNotConditionExpression

Use this option to specify — with a **lambda expression** — which property values should be compared.
If at least one property value is different, the delete is executed.

```csharp
context.BulkDelete(customers, options => 
{
    options.DeleteMatchedAndOneNotConditionExpression = x => new { x.Version };
});
```

| Method     | Option Name                               | Try it                                            |
| ---------- | ----------------------------------------- | ------------------------------------------------- |
| BulkDelete | DeleteMatchedAndOneNotConditionExpression | [Online Example](https://dotnetfiddle.net/lETrFX) |

---

## 🏷️ DeleteMatchedAndOneNotConditionNames

Use this option to specify — with a **list of property names** — which property values should be compared.
If at least one property value is different, the delete is executed.

```csharp
context.BulkDelete(customers, options => 
{
    options.DeleteMatchedAndOneNotConditionNames = new List<string>() 
    { 
        nameof(Customer.Version) 
    };
});
```

| Method     | Option Name                          | Try it                                            |
| ---------- | ------------------------------------ | ------------------------------------------------- |
| BulkDelete | DeleteMatchedAndOneNotConditionNames | [Online Example](https://dotnetfiddle.net/u3uEpb) |

---

## 🏷️ IgnoreOnDeleteMatchedAndOneNotConditionExpression

Use this option to specify — with a **lambda expression** — which property values should be excluded from comparison.
All other property values will be checked, and if at least one is different, the delete is executed.

```csharp
context.BulkDelete(customers, options => 
{
    options.IgnoreOnDeleteMatchedAndOneNotConditionExpression = x => new { x.CustomerID, x.Name, x.Email, x.Note };
});
```

| Method     | Option Name                                       | Try it                                            |
| ---------- | ------------------------------------------------- | ------------------------------------------------- |
| BulkDelete | IgnoreOnDeleteMatchedAndOneNotConditionExpression | [Online Example](https://dotnetfiddle.net/bNOJXF) |

---

## 🏷️ IgnoreOnDeleteMatchedAndOneNotConditionNames

Use this option to specify — with a **list of property names** — which property values should be excluded from comparison.
All other property values will be checked, and if at least one is different, the delete is executed.

```csharp
context.BulkDelete(customers, options => 
{
    options.IgnoreOnDeleteMatchedAndOneNotConditionNames = new List<string>() 
    { 
        nameof(Customer.CustomerID), nameof(Customer.Name), nameof(Customer.Email), nameof(Customer.Note) 
    };
});
```

| Method     | Option Name                                  | Try it                                            |
| ---------- | -------------------------------------------- | ------------------------------------------------- |
| BulkDelete | IgnoreOnDeleteMatchedAndOneNotConditionNames | [Online Example](https://dotnetfiddle.net/DFHMaU) |

---

## 🏁 Conclusion

Using `DeleteMatchedAndOneNotCondition` option in Entity Framework Extensions with EF Core ensures that **deletions only happen when at least one important value is different** between source and destination.

You can choose:

* **Expression-based include** → `DeleteMatchedAndOneNotConditionExpression`
* **Name-based include** → `DeleteMatchedAndOneNotConditionNames`
* **Expression-based exclude** → `IgnoreOnDeleteMatchedAndOneNotConditionExpression`
* **Name-based exclude** → `IgnoreOnDeleteMatchedAndOneNotConditionNames`

By using `DeleteMatchedAndOneNotCondition` in your [bulk operations](/bulk-extensions), you can:

* Skip unnecessary deletions when values match
* Protect sensitive or important records
* Keep synchronization safer and more predictable

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