---
Name: Delete Matched and Condition Option in Entity Framework Extensions
MetaDescription: Learn how to use the 'DeleteMatchedAndCondition' option in Entity Framework Extensions to control when deletes are executed. Ensure deletes only occur when source and destination values match for selected properties. See examples, scenarios, and four configuration methods.
LastMod: 2025-08-17
---

# DeleteMatchedAndCondition Options in EF Extensions

The `DeleteMatchedAndCondition` option in Entity Framework Extensions lets you **perform the `DELETE` action only when certain values in the source and destination are equal**.  

If the selected values don’t match, the delete is skipped. This is useful whenever you need more control over which rows get deleted — for example to **avoid removing newer records, protect versioned data, or enforce simple concurrency checks**.  

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

context.BulkDelete(customers, options => 
{
    // ON DELETE: only delete customers if "Version" has the same value in both source and destination
    options.DeleteMatchedAndConditionExpression = x => new { x.Version };
});
````

Typical scenarios include:

* **Row version (`Version`)** → Delete a record only if the version is the same in both source and destination (lightweight concurrency check).
* **Approval flag (`IsApproved`)** → Prevent deleting rows already approved by another process.
* **Status (`Status`)** → Only delete if both sides are marked as `Inactive`, avoiding accidental removal of active data.
* **Audit protection** → Ensure rows are deleted only when both match the expected audit values.

This option applies to the following method in Entity Framework Extensions:

* [BulkDelete](/bulk-delete)

---

## 💡 Example Effect

| **ID** | **Destination.Version** | **Source.Version** | **Without Condition** | **With Condition** |
| ------ | ----------------------- | ------------------ | --------------------- | ------------------ |
| 1      | 1                       | 1                  | deleted             | deleted          |
| 2      | 2                       | 3                  | deleted             | skipped          |
| 3      | 4                       | 2                  | deleted             | skipped          |

---

## 🛠️ Prerequisites

Before continuing, we recommend reading these articles first to understand **how EF Extensions options work** and the **differences between column option types**:

* [Configure Options](/configure-options) – Learn the basics of setting and customizing options in EF Extensions.
* [Configure Column Options](/configure-column-options) – See how to configure column-specific behavior and when to use `Expression` (strongly typed, compile-time safe) vs. `Names` (string-based, dynamic at runtime).

---

## 📌 When to Use

Use the `DeleteMatchedAndCondition` option from Entity Framework Extensions when:

* You want to **skip deletes for certain records** based on business rules
* Deletes should happen **only when both source and destination values match**
* Primary key filtering alone is not enough, and you need finer control

---

## ⭐ Why It’s Useful

Without `DeleteMatchedAndCondition`, using in EF Core a [BulkDelete](/bulk-delete) from Entity Framework Extensions could accidentally **remove rows that should not be deleted**.

Using this option, you can:

* Prevent unwanted deletions of newer or protected data
* Enforce concurrency rules without complex SQL
* Simplify your logic and keep your database safe

---

## 🏢 Scenario

A company imports in EF Core customers using [BulkDelete](/bulk-delete) method from Entity Framework Extensions.

In the example, customers have a `Version` column:

* If the **Version values are equal** → the customer can be deleted
* If the **Version values differ** → the customer must remain in the database

This ensures that only outdated or expected records are deleted, protecting against accidental removal of updated rows.

Other common cases include:

* Deleting orders only if the **Status values** are the same in both systems
* Deleting records only if the **IsApproved flag** is unchanged
* Deleting entities only when **Version values** match, preventing loss of concurrent changes

---

### 🗝️ Solution

The `DeleteMatchedAndCondition` option offers **four ways** to configure behavior:

* **[DeleteMatchedAndConditionExpression](#deletematchedandconditionexpression)** – include properties via a lambda expression
* **[DeleteMatchedAndConditionNames](#deletematchedandconditionnames)** – include properties via a list of property names
* **[IgnoreOnDeleteMatchedAndConditionExpression](#ignoreondeletematchedandconditionexpression)** – exclude properties via a lambda expression, all others properties are included.
* **[IgnoreOnDeleteMatchedAndConditionNames](#ignoreondeletematchedandconditionnames)** – exclude properties via a list of property names, all others properties are included.

---

## 🏷️ DeleteMatchedAndConditionExpression

Use this option to specify — with a **lambda expression** — which property values must be **equal in both source and destination** for the `DELETE` to occur.

If all selected property values match, the delete is executed. Otherwise, it is skipped.

```csharp
context.BulkDelete(customers, options => 
{
    // ON DELETE: remove customers only when "Version" values are equal in both source and destination
    options.DeleteMatchedAndConditionExpression = x => new { x.Version };
});
```

| Method     | Option Name                         | Try it                                            |
| ---------- | ----------------------------------- | ------------------------------------------------- |
| BulkDelete | DeleteMatchedAndConditionExpression | [Online Example](https://dotnetfiddle.net/kEVym8) |

---

## 🏷️ DeleteMatchedAndConditionNames

Use this option to specify — with a **list of property names** — which property values must be **equal in both source and destination** for the `DELETE` to occur.

If all selected property values match, the delete is executed. Otherwise, it is skipped.

```csharp
context.BulkDelete(customers, options => 
{
    // ON DELETE: perform delete only when "Version" values are equal in both source and destination
    options.DeleteMatchedAndConditionNames = new List<string>() { nameof(Customer.Version) };
});
```

| Method     | Option Name                    | Try it                                            |
| ---------- | ------------------------------ | ------------------------------------------------- |
| BulkDelete | DeleteMatchedAndConditionNames | [Online Example](https://dotnetfiddle.net/wVRpZR) |

---

## 🏷️ IgnoreOnDeleteMatchedAndConditionExpression

Use this option to specify — with a **lambda expression** — which property values should be **excluded from the equality check**.

All other property values (not listed) will be compared between source and destination. The `DELETE` will only happen if those remaining values are equal.

```csharp
context.BulkDelete(customers, options => 
{
    // ON DELETE: remove customer only when all values except these are equal
    options.IgnoreOnDeleteMatchedAndConditionExpression = x => new { x.CustomerID, x.Name, x.Email, x.Note };
});
```

| Method     | Option Name                                 | Try it                                            |
| ---------- | ------------------------------------------- | ------------------------------------------------- |
| BulkDelete | IgnoreOnDeleteMatchedAndConditionExpression | [Online Example](https://dotnetfiddle.net/b3oNfG) |

---

## 🏷️ IgnoreOnDeleteMatchedAndConditionNames

Use this option to specify — with a **list of property names** — which values should be **excluded from the equality check**.

All other values (not listed) will be compared between source and destination. The `DELETE` will only happen if those remaining values are equal.

```csharp
context.BulkDelete(customers, options => 
{
    // ON DELETE: remove customer only when all values except these are equal
    options.IgnoreOnDeleteMatchedAndConditionNames = new List<string>() 
    { 
        nameof(Customer.CustomerID), nameof(Customer.Name), nameof(Customer.Email), nameof(Customer.Note) 
    };
});
```

| Method     | Option Name                            | Try it                                            |
| ---------- | -------------------------------------- | ------------------------------------------------- |
| BulkDelete | IgnoreOnDeleteMatchedAndConditionNames | [Online Example](https://dotnetfiddle.net/D2BciE) |

---

## 🏁 Conclusion

Using `DeleteMatchedAndCondition` option in Entity Framework Extensions with EF Core ensures that **deletes only happen when specific source and destination values match**.

You can choose:

* **Expression-based include** → `DeleteMatchedAndConditionExpression`
* **Name-based include** → `DeleteMatchedAndConditionNames`
* **Expression-based exclude** → `IgnoreOnDeleteMatchedAndConditionExpression`
* **Name-based exclude** → `IgnoreOnDeleteMatchedAndConditionNames`

By using `DeleteMatchedAndCondition` in your [bulk operations](/bulk-extensions), you can:

* Prevent accidental deletion of important rows
* Fine-tune which records should or should not be deleted
* Simplify cleanup and synchronization logic while keeping your database safe

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