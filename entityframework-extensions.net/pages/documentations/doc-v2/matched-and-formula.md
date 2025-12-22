---
Name: Matched and Formula Option in Entity Framework Extensions
MetaDescription: Learn how to use the 'MatchedAndFormula' option in Entity Framework Extensions to update only when a custom SQL condition is true. Control updates with advanced logic such as version checks or business rules. See examples, scenarios, and configuration methods.
LastMod: 2025-08-17
---

# MatchedAndFormula Options in EF Extensions

<iframe width="560" height="315" src="https://www.youtube.com/embed/HiujI5LpEtE" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

The `MatchedAndFormula` option in Entity Framework Extensions lets you **perform the `UPDATE` action only when a custom SQL condition is true**.  

Use this option when your rule cannot be expressed with simple value comparisons and needs **SQL-level logic**.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

context.BulkMerge(customers, options => 
{
    // ON UPDATE: only update customers if the source version is higher than the destination version
    // StagingTable = source
    // DestinationTable = database
    options.MergeMatchedAndFormula = "StagingTable.Version > DestinationTable.Version";
});
````

> ⚠️ **Warning about SQL Injection**  
> The `MatchedAndFormula` option executes your SQL formula directly.  
> Avoid concatenating user input into the formula, as it may open the door to SQL injection.


**Typical scenarios include:**

* **Versioning (`Version`)** → Update only when `StagingTable.Version > DestinationTable.Version` to avoid overwriting newer data.
* **Recency (`ModifiedDate`)** → Update only when the source is more recent: `StagingTable.ModifiedDate > DestinationTable.ModifiedDate`.
* **Status gating (`Status`)** → Update only when both sides meet a rule, for example `StagingTable.Status = 'Active' AND DestinationTable.Status = 'Active'`.
* **Effective-dated data (`ValidFrom`)** → Update when the incoming row becomes effective: `StagingTable.ValidFrom <= GETUTCDATE()`.
* **Pricing rules (`Price`, `IsApproved`)** → Update only when the price changed and is approved: `StagingTable.Price <> DestinationTable.Price AND StagingTable.IsApproved = 1`.
* **Inventory protection (`Quantity`, `IsLocked`)** → Update stock only if not locked: `DestinationTable.IsLocked = 0`.

This option applies to the following methods in Entity Framework Extensions:

* [BulkUpdate](/bulk-update)
* [BulkMerge](/bulk-merge)
* [BulkSynchronize](/bulk-synchronize)

---

## 💡 Example Effect

For the following formula:  `options.MergeMatchedAndFormula = "StagingTable.Version > DestinationTable.Version";`

The update will only run when the **source version is higher** than the destination version:

| **ID** | **Destination.Version** | **Source.Version** | **Without Condition** | **With Condition** |
| ------ | ----------------------- | ------------------ | --------------------- | ------------------ |
| 1      | 1                       | 2                  | updated             | updated          |
| 2      | 3                       | 3                  | updated             | skipped          |
| 3      | 4                       | 2                  | updated             | skipped          |


---

## 🛠️ Prerequisites

Before continuing, we recommend reading these articles first to understand **how EF Extensions options work** and the **differences between column option types**:

* [Configure Options](/configure-options) – Learn the basics of setting and customizing options in EF Extensions.
* [Configure Column Options](/configure-column-options) – See how to configure column-specific behavior and when to use `Expression` (strongly typed, compile-time safe) vs. `Names` (string-based, dynamic at runtime).

---

## 📌 When to Use

Use `MatchedAndFormula` option from Entity Framework Extensions when:

* You need **advanced control** over when updates occur
* Your rule is easier to express as **SQL** (dates, statuses, joins/flags)
* You want to **enforce business rules** at the update step without changing app code

---

## ⭐ Why It’s Useful

Without `MatchedAndFormula`, using in EF Core a bulk operations like [BulkUpdate](/bulk-update), [BulkMerge](/bulk-merge), or [BulkSynchronize](/bulk-synchronize) from Entity Framework Extensions could **overwrite values that should remain unchanged**.

Using this option, you can:

* Enforce version-based or date-based rules
* Apply complex business logic directly in SQL
* Prevent unwanted overwrites with invalid or outdated data

---

## 🏢 Scenario

A company imports customers using [BulkMerge](/bulk-merge) method from Entity Framework Extensions in EF Core .

The rule: **only update a record if the source has a higher version than the database**.
This ensures that **older data never overwrites newer data**.

In summary:

* If the source `Version` is **greater** → update the customer
* If the source `Version` is **equal or lower** → skip the update

Other common cases:

* Update only if the source is **more recent** by `ModifiedDate`
* Update only when both sides are **`Active`**
* Update only when **approved** changes are incoming

---

### 🗝️ Solution

The `MatchedAndFormula` option provides **one configuration method**:

* **[[Action]MatchedAndFormula](#actionmatchedandformula)** → hardcode a SQL formula that returns `true` or `false`

---

## 🏷️ [Action]MatchedAndFormula

Use this option to specify a **SQL predicate** that decides if the update should occur.
If the predicate is `true`, the update runs. Otherwise, it is skipped.

```csharp
context.BulkMerge(customers, options => 
{
    // ON UPDATE: update only when the source version is greater than the destination version
    options.MergeMatchedAndFormula = "StagingTable.Version > DestinationTable.Version";
});
```

**Table aliases:**

* `DestinationTable` → table in the database
* `StagingTable` → incoming source data

| Method          | Option Name                  | Try it                                            |
| --------------- | ---------------------------- | ------------------------------------------------- |
| BulkMerge       | MergeMatchedAndFormula       | [Online Example](https://dotnetfiddle.net/sgEyDR) |
| BulkUpdate      | UpdateMatchedAndFormula      | [Online Example](https://dotnetfiddle.net/cTGNTh) |
| BulkSynchronize | SynchronizeMatchedAndFormula | [Online Example](https://dotnetfiddle.net/zsroHG) |

---

## 🏁 Conclusion

Using `MatchedAndFormula` option in Entity Framework Extensions with EF Core ensures that **updates only happen when your SQL condition is met**.

You can use it to:

* Enforce **version** or **date** rules
* Apply **status** or **approval** gates
* Keep your updates **safe, fast, and predictable**

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