---
Name: Matched and Condition Option in Entity Framework Extensions
MetaDescription: Learn how to use the 'MatchedAndCondition' option in Entity Framework Extensions to control when updates are executed. Ensure updates only occur when source and destination values match for selected properties. See examples, scenarios, and four configuration methods.
LastMod: 2025-08-17
---

# 🔒❓ Matched and Condition Option in Entity Framework Extensions /n Update only when source and destination values match

<iframe width="560" height="315" src="https://www.youtube.com/embed/O2k8QZ4RHdw" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

The `MatchedAndCondition` option in Entity Framework Extensions lets you **perform the `UPDATE` action only when certain values in the source and destination are equal**.  

If the selected values don’t match, the update is skipped. This is useful whenever you need more control over which rows get updated — for example to **avoid touching restricted records, skip already-approved entries, or enforce simple concurrency checks**.  

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

context.BulkMerge(customers, options => 
{
    // ON UPDATE: only update customers if "IsLocked" has the same value in both source and destination
    options.MergeMatchedAndConditionExpression = x => new { x.IsLocked };
});
````

Typical scenarios include:

* **Locked records (`IsLocked`)** → Don’t update customers that are marked as locked in the database.
* **Status field (`Status`)** → Only update if both records are still `Active`, skipping updates if the destination has been moved to `Archived` or `Closed`.
* **Approval date (`ApprovalDate`)** → Prevent updates once a row has been approved (only update when both are `null`).
* **Row version (`Version`)** → Use as a lightweight concurrency check — update only if both source and destination have the same version value.

This option applies to the following methods in Entity Framework Extensions:

* [BulkUpdate](/bulk-update)
* [BulkMerge](/bulk-merge)
* [BulkSynchronize](/bulk-synchronize)

---

## 💡 Example Effect

| **ID** | **Destination.IsLocked** | **Source.IsLocked** | **Without Condition** | **With Condition** |
| ------ | ------------------------ | ------------------- | --------------------- | ------------------ |
| 1      | 0 (unlocked)             | 0 (unlocked)        | updated             | updated          |
| 2      | 1 (locked)               | 0 (unlocked)        | updated             | skipped          |
| 3      | 0 (unlocked)             | 1 (locked)          | updated             | skipped          |

---

## 🛠️ Prerequisites

Before continuing, we recommend reading these articles first to understand **how EF Extensions options work** and the **differences between column option types**:

* [Configure Options](/configure-options) – Learn the basics of setting and customizing options in EF Extensions.
* [Configure Column Options](/configure-column-options) – See how to configure column-specific behavior and when to use `Expression` (strongly typed, compile-time safe) vs. `Names` (string-based, dynamic at runtime).

---

## 📌 When to Use

Use the `MatchedAndCondition` option from Entity Framework Extensions when:

* You want to **skip updates for certain records** based on business rules
* Updates should happen **only when both source and destination values match**
* Primary key filtering alone is not enough, and you need finer control

---

## ⭐ Why It’s Useful

Without `MatchedAndCondition`, using in EF Core a bulk operations like [BulkUpdate](/bulk-update), [BulkMerge](/bulk-merge), or [BulkSynchronize](/bulk-synchronize) from Entity Framework Extensions could accidentally **update rows that should not change**.

Using this option, you can:

* Protect sensitive or restricted data
* Avoid overwriting approved or finalized rows
* Simplify your logic without writing custom SQL conditions

---

## 🏢 Scenario

A company imports customers using [BulkMerge](/bulk-merge) method from Entity Framework Extensions in EF Core.

In the example, customers have an `IsLocked` column:

* `IsLocked = 0` → the customer can be updated
* `IsLocked = 1` → the customer must not be updated

All source customers have `IsLocked = 0`. Without precautions, locked customers could still be updated.

With the `MatchedAndCondition` option, updates only occur when both values match, ensuring locked customers stay unchanged.

Other common cases include:

* Updating orders only if the **Status values** are the same in both systems
* Updating records only if the **ApprovalDate values** are both `null`
* Updating entities only when the **Version values** match, to prevent overwriting concurrent changes

---

### 🗝️ Solution

The `MatchedAndCondition` option offers **four ways** to configure behavior:

* **[[Action]MatchedAndConditionExpression](#actionmatchedandconditionexpression)** – include properties via a lambda expression
* **[[Action]MatchedAndConditionNames](#actionmatchedandconditionnames)** – include properties via a list of property names
* **[IgnoreOn[Action]MatchedAndConditionExpression](#ignoreonactionmatchedandconditionexpression)** – exclude properties via a lambda expression, all others properties are included.
* **[IgnoreOn[Action]MatchedAndConditionNames](#ignoreonactionmatchedandconditionnames)** – exclude properties via a list of property names, all others properties are included.

---

## 🏷️ [Action]MatchedAndConditionExpression

Use this option to specify — with a **lambda expression** — which property values must be **equal in both source and destination** for the `UPDATE` to occur.

If all selected property values match, the update is executed. Otherwise, it is skipped.

```csharp
context.BulkMerge(customers, options => 
{
    // ON UPDATE: perform update only when "IsLocked" values are equal in both source and destination
    options.MergeMatchedAndConditionExpression = x => new { x.IsLocked };
});
```

| Method          | Option Name                              | Try it                                            |
| --------------- | ---------------------------------------- | ------------------------------------------------- |
| BulkMerge       | MergeMatchedAndConditionExpression       | [Online Example](https://dotnetfiddle.net/uci5RT) |
| BulkUpdate      | UpdateMatchedAndConditionExpression      | [Online Example](https://dotnetfiddle.net/NUwq90) |
| BulkSynchronize | SynchronizeMatchedAndConditionExpression | [Online Example](https://dotnetfiddle.net/yFY5tG) |

---

## 🏷️ [Action]MatchedAndConditionNames

Use this option to specify — with a **list of property names** — which property values must be **equal in both source and destination** for the `UPDATE` to occur.

If all selected property values match, the update is executed. Otherwise, it is skipped.

```csharp
context.BulkMerge(customers, options => 
{
    // ON UPDATE: perform update only when "IsLocked" values are equal in both source and destination
    options.MergeMatchedAndConditionNames = new List<string>() { nameof(Customer.IsLocked) };
});
```

| Method          | Option Name                         | Try it                                            |
| --------------- | ----------------------------------- | ------------------------------------------------- |
| BulkMerge       | MergeMatchedAndConditionNames       | [Online Example](https://dotnetfiddle.net/U7t1PU) |
| BulkUpdate      | UpdateMatchedAndConditionNames      | [Online Example](https://dotnetfiddle.net/ulHCGM) |
| BulkSynchronize | SynchronizeMatchedAndConditionNames | [Online Example](https://dotnetfiddle.net/KiNuqb) |

---

## 🏷️ IgnoreOn[Action]MatchedAndConditionExpression

Use this option to specify — with a **lambda expression** — which property values should be **excluded from the equality check**.

All other property values (not listed) will be compared between source and destination. The `UPDATE` will only happen if those remaining values are equal.

```csharp
context.BulkMerge(customers, options => 
{
    // ON UPDATE: perform update only when all values except these are equal
    options.IgnoreOnMergeMatchedAndConditionExpression = x => new { x.CustomerID, x.Name, x.Email };
});
```

| Method          | Option Name                                      | Try it                                            |
| --------------- | ------------------------------------------------ | ------------------------------------------------- |
| BulkMerge       | IgnoreOnMergeMatchedAndConditionExpression       | [Online Example](https://dotnetfiddle.net/67SGs7) |
| BulkUpdate      | IgnoreOnUpdateMatchedAndConditionExpression      | [Online Example](https://dotnetfiddle.net/PXlcOi) |
| BulkSynchronize | IgnoreOnSynchronizeMatchedAndConditionExpression | [Online Example](https://dotnetfiddle.net/Zi6dzI) |

---

## 🏷️ IgnoreOn[Action]MatchedAndConditionNames

Use this option to specify — with a **list of property names** — which property values should be **excluded from the equality check**.

All other property values (not listed) will be compared between source and destination. The `UPDATE` will only happen if those remaining values are equal.

```csharp
context.BulkMerge(customers, options => 
{
    // ON UPDATE: perform update only when all values except these are equal
    options.IgnoreOnMergeMatchedAndConditionNames = new List<string>() 
    { 
        nameof(Customer.CustomerID), nameof(Customer.Name), nameof(Customer.Email) 
    };
});
```

| Method          | Option Name                                 | Try it                                            |
| --------------- | ------------------------------------------- | ------------------------------------------------- |
| BulkMerge       | IgnoreOnMergeMatchedAndConditionNames       | [Online Example](https://dotnetfiddle.net/WdSS7H) |
| BulkUpdate      | IgnoreOnUpdateMatchedAndConditionNames      | [Online Example](https://dotnetfiddle.net/8Y4gis) |
| BulkSynchronize | IgnoreOnSynchronizeMatchedAndConditionNames | [Online Example](https://dotnetfiddle.net/ippun6) |

---

## 🏁 Conclusion

Using `MatchedAndCondition` option in Entity Framework Extensions with EF Core ensures that **updates only happen when specific source and destination values match**.

You can choose:

* **Expression-based include** → `[Action]MatchedAndConditionExpression`
* **Name-based include** → `[Action]MatchedAndConditionNames`
* **Expression-based exclude** → `IgnoreOn[Action]MatchedAndConditionExpression`
* **Name-based exclude** → `IgnoreOn[Action]MatchedAndConditionNames`

By using `MatchedAndCondition` in your [bulk operations](/bulk-extensions):

* Prevent unwanted updates when values don’t meet your business rules
* Fine-tune which records should or should not be updated
* Simplify synchronization logic and keep your database consistent

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