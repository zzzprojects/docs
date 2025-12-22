---
Name: Delete Matched and Formula Option in Entity Framework Extensions
MetaDescription: Learn how to use the 'DeleteMatchedAndFormula' option in Entity Framework Extensions to delete only when a custom SQL condition is true. Control deletions with advanced logic such as version checks or business rules. See examples, scenarios, and configuration methods.
LastMod: 2025-08-17
---

# DeleteMatchedAndFormula Options in EF Extensions

The `DeleteMatchedAndFormula` option in Entity Framework Extensions lets you **perform the `DELETE` action only when a custom SQL condition is true**.  

Use this option when your delete logic goes beyond simple key matches and requires **SQL-level rules**.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

context.BulkDelete(customers, options => 
{
    // REQUIRED: by default, only "CustomerID" (they Key) is included in the staging table
    options.ColumnStagingTableFormulaExpression = x => x.Version;

    // ON DELETE: remove customer only if the source version is greater
    options.DeleteMatchedAndFormula = "StagingTable.Version > DestinationTable.Version";
});
````

> ⚠️ **Warning about SQL Injection**  
> The `DeleteMatchedAndFormula` option executes your SQL formula directly.  
> Avoid concatenating user input into the formula, as it may open the door to SQL injection.

Typical scenarios include:

* **Versioning (`Version`)** → Delete only when the source has a higher version: `StagingTable.Version > DestinationTable.Version`.
* **Recency (`ModifiedDate`)** → Delete only when the source is more recent: `StagingTable.ModifiedDate > DestinationTable.ModifiedDate`.
* **Expiration (`ValidUntil`)** → Delete only expired rows: `DestinationTable.ValidUntil < GETUTCDATE()`.
* **Status checks (`Status`)** → Delete only inactive or disabled rows: `DestinationTable.Status = 'Inactive'`.
* **Approval gating (`IsApproved`)** → Delete only when flagged for removal: `StagingTable.IsApproved = 1`.
* **Inventory cleanup (`Quantity`, `IsObsolete`)** → Delete stock items that are obsolete or not locked: `DestinationTable.IsObsolete = 1 AND DestinationTable.IsLocked = 0`.

This option applies to the following methods in Entity Framework Extensions:

* [BulkDelete](/bulk-delete)

---

## 💡 Example Effect

For the following formula:
`options.DeleteMatchedAndFormula = "StagingTable.Version > DestinationTable.Version";`

The delete will only run when the **source version is higher** than the destination version:

| **ID** | **Destination.Version** | **Source.Version** | **Without Condition** | **With Condition** |
| ------ | ----------------------- | ------------------ | --------------------- | ------------------ |
| 1      | 1                       | 2                  | deleted             | deleted          |
| 2      | 3                       | 3                  | deleted             | skipped          |
| 3      | 4                       | 2                  | deleted             | skipped          |

---

## 🛠️ Prerequisites

Before continuing, we recommend reading these articles first to understand **how EF Extensions options work** and the **differences between column option types**:

* [Configure Options](/configure-options) – Learn the basics of setting and customizing options in EF Extensions.
* [Configure Column Options](/configure-column-options) – See how to configure column-specific behavior and when to use `Expression` (strongly typed, compile-time safe) vs. `Names` (string-based, dynamic at runtime).

---

## 📌 When to Use

Use `DeleteMatchedAndFormula` option from Entity Framework Extensions when:

* You need **advanced control** over when deletions occur
* Your rule is easier to express as **SQL** (versions, dates, statuses)
* You want to **enforce business rules** directly in the deletion step

---

## ⭐ Why It’s Useful

Without `DeleteMatchedAndFormula`, using in EF Core a [BulkDelete](/bulk-delete) method from Entity Framework Extensions could remove rows that should remain in your database.

Using this option, you can:

* Enforce **version-based or date-based rules**
* Apply **status** or **approval** checks before deleting
* Prevent **accidental deletion** of valuable data

---

## 🏢 Scenario

A company imports in EF Core customers and uses [BulkDelete](/bulk-delete) from Entity Framework Extensions.

The rule: **only delete a record if the source has a higher version than the database**.
This ensures that **older imports cannot delete newer records**.

In summary:

* If the source `Version` is **greater** → delete the customer
* If the source `Version` is **equal or lower** → keep the customer

Other common cases:

* Delete only if the source row is **more recent**
* Delete only when both sides are **inactive**
* Delete only if a record is **expired or unapproved**

---

### 🗝️ Solution

The `DeleteMatchedAndFormula` option **one configuration method**:

* **[DeleteMatchedAndFormula](#deletematchedandformula)** → hardcode a SQL formula that returns `true` or `false`

---

## 🏷️ DeleteMatchedAndFormula

Use this option to specify a **SQL predicate** that decides if the delete should occur.
If the predicate is `true`, the row is deleted. Otherwise, it is skipped.

```csharp
context.BulkDelete(customers, options => 
{
    options.ColumnStagingTableFormulaExpression = x => x.Version;
    options.DeleteMatchedAndFormula = "StagingTable.Version > DestinationTable.Version";
});
```

**Table aliases:**

* `DestinationTable` → table in the database
* `StagingTable` → incoming source data

| Method     | Option Name             | Try it                                            |
| ---------- | ----------------------- | ------------------------------------------------- |
| BulkDelete | DeleteMatchedAndFormula | [Online Example](https://dotnetfiddle.net/mwjtvN) |

---

## 🏁 Conclusion

Using `DeleteMatchedAndFormula` option in Entity Framework Extensions with EF Core ensures that **deletes only happen when your SQL condition is met**.

You can use it to:

* Enforce **version** or **date** rules
* Apply **status** or **approval** gates
* Keep your deletions **safe, predictable, and aligned with business rules**

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