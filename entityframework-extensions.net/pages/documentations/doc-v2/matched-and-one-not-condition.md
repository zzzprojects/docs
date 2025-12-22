---
Name: Matched and One NOT Condition Option in Entity Framework Extensions
MetaDescription: Learn how to use the 'MatchedAndOneNotCondition' option in Entity Framework Extensions to update only when at least one value between source and destination is different. See examples, scenarios, and four configuration methods.
LastMod: 2025-08-17
---

# MatchedAndOneNotCondition Options in EF Extensions

<iframe width="560" height="315" src="https://www.youtube.com/embed/jCgnIVF0g-8" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

The `MatchedAndOneNotCondition` option in Entity Framework Extensions lets you **perform the `UPDATE` action only when at least one selected value in the source and destination is different**.  

If all selected values are equal, the update is skipped. This gives you more precise control over when updates should happen — for example, to **ignore insignificant changes, update only important fields, or optimize imports by avoiding unnecessary updates**.  

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

context.BulkMerge(customers, options => 
{
    // ON UPDATE: only update if "Name" or "Email" is different between source and destination
    options.MergeMatchedAndOneNotConditionExpression = x => new { x.Name, x.Email };
});
````

Typical scenarios include:

* **Audit fields (`ModifiedDate`, `ModifiedBy`)** → Only update when at least one of these changes, skipping updates that don’t touch metadata.
* **Contact info (`Phone`, `Email`)** → Ensure updates are made only when at least one contact detail is different, ignoring other non-critical fields.
* **Product pricing (`Price`, `Discount`)** → Update records only if the pricing-related values have changed, skipping cosmetic changes like `Description`.
* **User profile (`Name`, `Email`, `Role`)** → Prevent unnecessary updates unless a meaningful field like name, email, or role has been modified.

This option applies to the following methods in Entity Framework Extensions:

* [BulkUpdate](/bulk-update)
* [BulkMerge](/bulk-merge)
* [BulkSynchronize](/bulk-synchronize)

---

## 💡 Example Effect

| **ID** | **Destination.Name** | **Source.Name** | **Destination.Email**           | **Source.Email**                    | **Without Condition** | **With Condition** |
| ------ | -------------------- | --------------- | ------------------------------- | ----------------------------------- | --------------------- | ------------------ |
| 1      | John                 | John            | [john@a.com](mailto:john@a.com) | [john@a.com](mailto:john@a.com)     | updated             | skipped          |
| 2      | John                 | Johnny          | [john@a.com](mailto:john@a.com) | [john@a.com](mailto:john@a.com)     | updated             | updated          |
| 3      | John                 | John            | [john@a.com](mailto:john@a.com) | [johnny@a.com](mailto:johnny@a.com) | updated             | updated          |

---

## 🛠️ Prerequisites

Before continuing, we recommend reading these articles first to understand **how EF Extensions options work** and the **differences between column option types**:

* [Configure Options](/configure-options) – Learn the basics of setting and customizing options in EF Extensions.
* [Configure Column Options](/configure-column-options) – See how to configure column-specific behavior and when to use `Expression` (strongly typed, compile-time safe) vs. `Names` (string-based, dynamic at runtime).

---

## 📌 When to Use

Use the `MatchedAndOneNotCondition` option from Entity Framework Extensions when:

* You want to **update only if at least one key field has changed**
* You need to **ignore changes in unimportant fields**
* You want to **optimize synchronization** by skipping updates when nothing meaningful has changed

---

## ⭐ Why It’s Useful

Without `MatchedAndOneNotCondition`, using in EF Core a bulk operations like [BulkUpdate](/bulk-update), [BulkMerge](/bulk-merge), or [BulkSynchronize](/bulk-synchronize) from Entity Framework Extensions could **update rows unnecessarily**, even when the data hasn’t really changed.

Using this option, you can:

* Prevent redundant updates
* Focus updates only on meaningful differences
* Simplify logic and avoid writing custom SQL

---

## 🏢 Scenario

A company imports customers using [BulkMerge](/bulk-merge) method from Entity Framework Extensions in EF Core.

* The `Note` column often changes, but it’s not important enough to trigger an update.
* The system should only update a row when critical fields like `Name` or `Email` are different.

In summary:

* If `Name` or `Email` differs → update the customer
* If both are equal → skip the update, even if other fields (like `Note`) changed

---

### 🗝️ Solution

The `MatchedAndOneNotCondition` option offers **four ways** to configure behavior:

* **[[Action]MatchedAndOneNotConditionExpression](#actionmatchedandonenotconditionexpression)** – include properties via a lambda expression
* **[[Action]MatchedAndOneNotConditionNames](#actionmatchedandonenotconditionnames)** – include properties via a list of property names
* **[IgnoreOn[Action]MatchedAndOneNotConditionExpression](#ignoreonactionmatchedandonenotconditionexpression)** – exclude properties via a lambda expression, all others properties are included.
* **[IgnoreOn[Action]MatchedAndOneNotConditionNames](#ignoreonactionmatchedandonenotconditionnames)** – exclude properties via a list of property names, all others properties are included.

---

## 🏷️ [Action]MatchedAndOneNotConditionExpression

Use this option to specify — with a **lambda expression** — which property values should be compared.
If at least one property value is different, the update is executed.

```csharp
context.BulkMerge(customers, options => 
{
    // ON UPDATE: perform update only when "Name" or "Email" differ between source and destination
    options.MergeMatchedAndOneNotConditionExpression = x => new { x.Name, x.Email };
});
```

| Method          | Option Name                                    | Try it                                            |
| --------------- | ---------------------------------------------- | ------------------------------------------------- |
| BulkMerge       | MergeMatchedAndOneNotConditionExpression       | [Online Example](https://dotnetfiddle.net/LQZuak) |
| BulkUpdate      | UpdateMatchedAndOneNotConditionExpression      | [Online Example](https://dotnetfiddle.net/noelqT) |
| BulkSynchronize | SynchronizeMatchedAndOneNotConditionExpression | [Online Example](https://dotnetfiddle.net/F7nbwA) |

---

## 🏷️ [Action]MatchedAndOneNotConditionNames

Use this option to specify — with a **list of property names** — which property values should be compared.
If at least one property value is different, the update is executed.

```csharp
context.BulkMerge(customers, options => 
{
    options.MergeMatchedAndOneNotConditionNames = new List<string>() 
    { 
        nameof(Customer.Name), nameof(Customer.Email) 
    };
});
```

| Method          | Option Name                               | Try it                                            |
| --------------- | ----------------------------------------- | ------------------------------------------------- |
| BulkMerge       | MergeMatchedAndOneNotConditionNames       | [Online Example](https://dotnetfiddle.net/GFjZI3) |
| BulkUpdate      | UpdateMatchedAndOneNotConditionNames      | [Online Example](https://dotnetfiddle.net/lq50C0) |
| BulkSynchronize | SynchronizeMatchedAndOneNotConditionNames | [Online Example](https://dotnetfiddle.net/YYN2uZ) |

---

## 🏷️ IgnoreOn[Action]MatchedAndOneNotConditionExpression

Use this option to specify — with a **lambda expression** — which property values should be excluded from comparison.
All other property values will be checked, and if at least one is different, the update is executed.

```csharp
context.BulkMerge(customers, options => 
{
    // Exclude Note → it won’t trigger updates
    options.IgnoreOnMergeMatchedAndOneNotConditionExpression = x => new { x.Note };
});
```

| Method          | Option Name                                            | Try it                                            |
| --------------- | ------------------------------------------------------ | ------------------------------------------------- |
| BulkMerge       | IgnoreOnMergeMatchedAndOneNotConditionExpression       | [Online Example](https://dotnetfiddle.net/XqgHKo) |
| BulkUpdate      | IgnoreOnUpdateMatchedAndOneNotConditionExpression      | [Online Example](https://dotnetfiddle.net/65T8kP) |
| BulkSynchronize | IgnoreOnSynchronizeMatchedAndOneNotConditionExpression | [Online Example](https://dotnetfiddle.net/zGSrJR) |

---

## 🏷️ IgnoreOn[Action]MatchedAndOneNotConditionNames

Use this option to specify — with a **list of property names** — which property values should be excluded from comparison.
All other property values will be checked, and if at least one is different, the update is executed.

```csharp
context.BulkMerge(customers, options => 
{
    options.IgnoreOnMergeMatchedAndOneNotConditionNames = new List<string>() { nameof(Customer.Note) };
});
```

| Method          | Option Name                                       | Try it                                            |
| --------------- | ------------------------------------------------- | ------------------------------------------------- |
| BulkMerge       | IgnoreOnMergeMatchedAndOneNotConditionNames       | [Online Example](https://dotnetfiddle.net/aSiWOu) |
| BulkUpdate      | IgnoreOnUpdateMatchedAndOneNotConditionNames      | [Online Example](https://dotnetfiddle.net/4FZyj5) |
| BulkSynchronize | IgnoreOnSynchronizeMatchedAndOneNotConditionNames | [Online Example](https://dotnetfiddle.net/03ayTe) |

---

## 🏁 Conclusion

Using `MatchedAndOneNotCondition` option in Entity Framework Extensions with EF Core ensures that **updates only happen when at least one important value is different** between source and destination.

You can choose:

* **Expression-based include** → `[Action]MatchedAndOneNotConditionExpression`
* **Name-based include** → `[Action]MatchedAndOneNotConditionNames`
* **Expression-based exclude** → `IgnoreOn[Action]MatchedAndOneNotConditionExpression`
* **Name-based exclude** → `IgnoreOn[Action]MatchedAndOneNotConditionNames`

By using `MatchedAndOneNotCondition` in your [bulk operations](/bulk-extensions):

* Skip redundant updates when values are identical
* Focus updates only on fields that matter
* Keep imports and synchronizations faster and more predictable

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