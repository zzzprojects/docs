---
Name: Coalesce Destination Option in Entity Framework Extensions
MetaDescription: Learn how to use the 'CoalesceDestination' option in Entity Framework Extensions to update null values in the database during bulk operations without overwriting existing non-null values. See examples, scenarios, and three configuration methods to keep your data accurate and consistent.
LastMod: 2025-08-17
---

# 🛡️ Coalesce Destination Option in Entity Framework Extensions /n Update only NULL values

The `CoalesceDestination` option in Entity Framework Extensions lets you **update the destination value only when the current value in the database is null**.

It’s equivalent to using `ISNULL(DestinationTable.ColumnName, StagingTable.ColumnName)` in SQL Server.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

context.BulkMerge(customers, options => 
{
	// ON UPDATE: change "Name" and "Email" only if the destination value is null
	options.CoalesceDestinationOnMergeUpdateExpression = x => new { x.Name, x.Email };
});
```

This option applies to the following methods in Entity Framework Extensions:

* [BulkUpdate](/bulk-update)
* [BulkMerge](/bulk-merge)
* [BulkSynchronize](/bulk-synchronize)

---

## 💡 Example Effect

| **ID** | **Destination.Name** | **Source.Name** | **Without Coalesce Destination** | **With Coalesce Destination** |
| ------ | -------------------- | --------------- | -------------------------------- | ----------------------------- |
| 1      | *(null)*             | John Smith      | John Smith                       | John Smith                    |
| 2      | Alice Brown          | Alice Brown     | Alice Brown                      | Alice Brown                   |
| 3      | David Wilson         | David Miller    | David Miller                     | David Wilson                  |

---

## 🛠️ Prerequisites

Before continuing, we recommend reading these articles first to understand **how EF Extensions options work** and the **differences between column option types**:

* [Configure Options](/configure-options) – Learn the basics of setting and customizing options in EF Extensions.
* [Configure Column Options](/configure-column-options) – See how to configure column-specific behavior and when to use `Expression` (strongly typed, compile-time safe) vs. `Names` (string-based, dynamic at runtime).

---

## 📌 When to Use

Use the `CoalesceDestination` option from Entity Framework Extensions when you want to **fill in missing values** in your database without replacing existing non-null values.

For example:

* Importing data from an external system and only filling blanks in the database.
* Updating customer profiles with optional data without overwriting already stored values.
* Merging staging table data into the main table and preserving any non-null values already stored.

---

## ⭐ Why It’s Useful

Without `CoalesceDestination`, using in EF Core a bulk operations like [BulkUpdate](/bulk-update), [BulkMerge](/bulk-merge), or [BulkSynchronize](/bulk-synchronize) from Entity Framework Extensions could **overwrite non-null values with new values** — even if the new data should only be applied when the destination is null.

Using this option ensures:

* Existing data is preserved if already populated.
* You avoid accidental overwriting of valid values.
* Your updates remain intentional and clean, without extra custom SQL.

---

## 🏢 Scenario

A company uses EF Core and imports customers with the [BulkMerge](/bulk-merge) method from Entity Framework Extensions to insert new customers and update existing ones.

However, the requirement is that **only null fields in the database should be updated** — all non-null values must be preserved.

In summary:

* When the destination `Email IS NULL`, the value is **updated**
* When the destination `Email IS NOT NULL`, the value is **kept**

---

### 🗝️ Solution

The `CoalesceDestination` option offers **three ways** to handle this scenario:

* **[On[Action]UseCoalesceDestination](#onactionusecoalescedestination)** – Apply `CoalesceDestination` to **all properties** during the operation.
* **[CoalesceDestinationOn[Action]Expression](#coalescedestinationonactionexpression)** – Apply `CoalesceDestination` only to **specific properties**, defined via a strongly typed lambda expression. Great for compile-time safety and easy refactoring.
* **[CoalesceDestinationOn[Action]Names](#coalescedestinationonactionnames)** – Apply `CoalesceDestination` only to **specific properties by name** (string list). Perfect when the list of properties is dynamic or comes from configuration.

---

## 🏷️ On[Action]UseCoalesceDestination

Use this option if you want **all properties** to apply the `CoalesceDestination` behavior by default.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

context.BulkMerge(customers, options => 
{
	// ON UPDATE: modify column values only if the destination value is null
	options.OnMergeUpdateUseCoalesceDestination = true;
});
```

| Method          | Option Name                               | Try it                                            |
| --------------- | ----------------------------------------- | ------------------------------------------------- |
| BulkMerge       | OnMergeUpdateUseCoalesceDestination       | [Online Example](https://dotnetfiddle.net/QKbtqe) |
| BulkUpdate      | OnUpdateUseCoalesceDestination            | [Online Example](https://dotnetfiddle.net/yyygQl) |
| BulkSynchronize | OnSynchronizeUpdateUseCoalesceDestination | [Online Example](https://dotnetfiddle.net/0UV1zX) |

---

## 🏷️ CoalesceDestinationOn[Action]Expression

Use this option if you want to **choose specific properties** that should use the `CoalesceDestination` behavior, defined via a lambda expression.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

context.BulkMerge(customers, options => 
{
	// ON UPDATE: change "Name" and "Email" only if the destination value is null
	options.CoalesceDestinationOnMergeUpdateExpression = x => new { x.Name, x.Email };
});
```

| Method          | Option Name                                      | Try it                                            |
| --------------- | ------------------------------------------------ | ------------------------------------------------- |
| BulkMerge       | CoalesceDestinationOnMergeUpdateExpression       | [Online Example](https://dotnetfiddle.net/RMw9fq) |
| BulkUpdate      | CoalesceDestinationOnUpdateExpression            | [Online Example](https://dotnetfiddle.net/1jfmno) |
| BulkSynchronize | CoalesceDestinationOnSynchronizeUpdateExpression | [Online Example](https://dotnetfiddle.net/Hacmqr) |

---

## 🏷️ CoalesceDestinationOn[Action]Names

Use this option if you prefer to **explicitly list the property names** that should use the `CoalesceDestination` behavior.

The value must match either the **property name** or a **navigation property name**.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

context.BulkMerge(customers, options => 
{
	options.CoalesceDestinationOnMergeUpdateNames = new List<string>() 
	{
		nameof(Customer.Name),
		nameof(Customer.Email)
	};
});
```

| Method          | Option Name                                 | Try it                                            |
| --------------- | ------------------------------------------- | ------------------------------------------------- |
| BulkMerge       | CoalesceDestinationOnMergeUpdateNames       | [Online Example](https://dotnetfiddle.net/AppZ13) |
| BulkUpdate      | CoalesceDestinationOnUpdateNames            | [Online Example](https://dotnetfiddle.net/rBhOY1) |
| BulkSynchronize | CoalesceDestinationOnSynchronizeUpdateNames | [Online Example](https://dotnetfiddle.net/AUII0Q) |

---

## 🏁 Conclusion

Using `CoalesceDestination` option in Entity Framework Extensions with EF Core is a targeted safeguard that ensures **only null fields in the database get updated**, leaving non-null values untouched.

You can choose:

* **All properties** → `On[Action]UseCoalesceDestination`
* **Specific properties via expressions** → `CoalesceDestinationOn[Action]Expression`
* **Specific properties by name** → `CoalesceDestinationOn[Action]Names`

By using `CoalesceDestination` in your [bulk operations](/bulk-extensions), you can:

* Preserve valuable data already stored
* Fill in missing information without overwriting valid values
* Maintain data consistency during imports and synchronizations

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