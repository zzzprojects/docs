---
Name: Coalesce Option in Entity Framework Extensions
MetaDescription: Learn how to use the Coalesce option in Entity Framework Extensions to prevent overwriting existing values with null during bulk operations. See examples, scenarios, and three configuration methods to keep your data safe and consistent.
LastMod: 2025-08-13
---

# Coalesce Option in Entity Framework Extensions /n Prevent null overwrites during updates

The `Coalesce` option in Entity Framework Extensions lets you **keep the destination value** when the source value is `null`.

It’s equivalent to using `ISNULL(StagingTable.ColumnName, DestinationTable.ColumnName)` in SQL Server.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

context.BulkMerge(customers, options => 
{
	// ON UPDATE: change "Name" and "Email" only if the source value is not null
	options.CoalesceOnMergeUpdateExpression = x => new { x.Name, x.Email };
});
```

This option applies to the following methods in Entity Framework Extensions:

* [BulkUpdate](/bulk-update)
* [BulkMerge](/bulk-merge)
* [BulkSynchronize](/bulk-synchronize)

---

## Example Effect

| **ID** | **Destination.Name** | **Source.Name** | **Without Coalesce** | **With Coalesce** |
| ------ | -------------------- | --------------- | -------------------- | ----------------- |
| 1      | John Smith           | *(null)*        | *(null)*             | John Smith        |
| 2      | Alice Brown          | Alice Brown     | Alice Brown          | Alice Brown       |
| 3      | David Wilson         | David Miller    | David Miller         | David Miller      |

---

## Prerequisites

Before continuing, we recommend reading these articles first to understand **how EF Extensions options work** and the **differences between column option types**:

* [Configure Options](/configure-options) – Learn the basics of setting and customizing options in EF Extensions.
* [Configure Column Options](/configure-column-options) – See how to configure column-specific behavior and when to use `Expression` (strongly typed, compile-time safe) vs. `Names` (string-based, dynamic at runtime).


---
## When to Use

Use the `Coalesce` option when importing or synchronizing data that **may have missing values**.
For example:

* Importing data from an external system that doesn’t always send all fields.
* Updating customer records while preserving existing values if the new data is incomplete.
* Merging staging table data into the main table without overwriting valid values with `null`.

---

## Why It’s Useful

Without `Coalesce`, a [BulkUpdate](/bulk-update), [BulkMerge](/bulk-merge), or [BulkSynchronize](/bulk-synchronize) could **overwrite existing data with null values** from the source.
Using this option ensures:

* Important data is preserved when the source doesn’t provide a value.
* Fewer accidental data losses when doing large merges.
* Cleaner data handling logic, without writing custom SQL.

---

## Scenario

A company uses EF Core and imports customers with the [BulkMerge](/bulk-merge) method from Entity Framework Extensions to insert new customers and update existing ones.

However, there’s a peculiarity: the customer data to import doesn’t always contain all values — some fields can be `null`. For certain columns, we want to **keep the destination value** if the source value is `null`.

In summary:

* When the source `Email IS NULL`, the destination value is **kept**
* When the source `Email IS NOT NULL`, the destination value is **updated**

---

### Solution

The `Coalesce` option offers **three ways** to handle this scenario, depending on how broadly or precisely you want to apply the behavior:

* **[On[Action]UseCoalesce](#onactionusecoalesce)** – Apply `Coalesce` to **all properties** during the operation. Quick to set up and ideal when you want maximum protection against overwriting with `null`.
* **[CoalesceOn[Action]Expression](#coalesceonactionexpression)** – Apply `Coalesce` only to **specific properties**, defined via a strongly typed lambda expression. Great for compile-time safety and easy refactoring.
* **[CoalesceOn[Action]Names](#coalesceonactionnames)** – Apply `Coalesce` only to **specific properties by name** (string list). Perfect when the list of properties is dynamic or comes from configuration.


# On[Action]UseCoalesce

Use this option if you want **all properties** to apply the `Coalesce` behavior by default.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

context.BulkMerge(customers, options => 
{
	// ON UPDATE: modify column values only if the source value is not null
	options.OnMergeUpdateUseCoalesce = true;
});
```

| Method          | Option Name                    | Try it                                    |
| --------------- | ------------------------------ | ----------------------------------------- |
| BulkMerge       | OnMergeUpdateUseCoalesce       | [Online Example](https://dotnetfiddle.net/nPOanO) |
| BulkUpdate      | OnUpdateUseCoalesce            | [Online Example](https://dotnetfiddle.net/W6Ijmi) |
| BulkSynchronize | OnSynchronizeUpdateUseCoalesce | [Online Example](https://dotnetfiddle.net/pfbBXy) |

## CoalesceOn[Action]Expression

Use this option if you want to **choose specific properties** that should use the `Coalesce` behavior, defined via a lambda expression.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

context.BulkMerge(customers, options => 
{
	// ON UPDATE: change "Name" and "Email" only if the source value is not null
	options.CoalesceOnMergeUpdateExpression = x => new { x.Name, x.Email };
});
```

| Method          | Option Name                           | Try it                                    |
| --------------- | ------------------------------------- | ----------------------------------------- |
| BulkMerge       | CoalesceOnMergeUpdateExpression       | [Online Example](https://dotnetfiddle.net/UyQh2O) |
| BulkUpdate      | CoalesceOnUpdateExpression            | [Online Example](https://dotnetfiddle.net/IZyujj) |
| BulkSynchronize | CoalesceOnSynchronizeUpdateExpression | [Online Example](https://dotnetfiddle.net/mQtTtg) |


## CoalesceOn[Action]Names

Use this option if you prefer to **explicitly list the property names** that should use the `Coalesce` behavior.
The value must match either the **property name** or a **navigation property name**.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

context.BulkMerge(customers, options => 
{
	// ON UPDATE: change "Name" and "Email" only if the source value is not null
	options.CoalesceOnMergeUpdateNames = new List<string>() 
	{ 
		nameof(Customer.Name), 
		nameof(Customer.Email) 
	};
});
```

| Method          | Option Name                      | Try it                                    |
| --------------- | -------------------------------- | ----------------------------------------- |
| BulkMerge       | CoalesceOnMergeUpdateNames       | [Online Example](https://dotnetfiddle.net/BOxIWU) |
| BulkUpdate      | CoalesceOnUpdateNames            | [Online Example](https://dotnetfiddle.net/96yns2) |
| BulkSynchronize | CoalesceOnSynchronizeUpdateNames | [Online Example](https://dotnetfiddle.net/mmx8xY) |

## Which Coalesce option should you use?

| Situation / Goal                                                                        | Best Choice                                                 | Why                                                              | Notes                                                                                                  |
| --------------------------------------------------------------------------------------- | ----------------------------------------------------------- | ---------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------ |
| You want the `Coalesce` behavior applied to **all properties** during an update         | **On[Action]UseCoalesce**                                   | Fastest to configure. One flag enables the safeguard everywhere. | Good default for imports where many fields might be `null`. Example: `OnMergeUpdateUseCoalesce`.       |
| You only want `Coalesce` on **a few properties** and you prefer **strong typing**       | **CoalesceOn[Action]Expression**                            | Uses a lambda, so refactoring is safe and discoverable in IDEs.  | Best for code-first teams. Example: `x => new { x.Name, x.Email }`.                                    |
| The list of properties is **dynamic at runtime** (from config, user input, or metadata) | **CoalesceOn[Action]Names**                                 | Accepts a `List<string>`, easy to build from external sources.   | Use `nameof(...)` when possible to avoid typos. Example: `new List<string> { nameof(Customer.Name) }`. |
| You are prototyping and want a **quick switch** before fine-tuning later                | **On[Action]UseCoalesce** → then narrow with **Expression** | Start broad to stay safe, then lock down the exact fields.       | Helps prevent accidental null overwrites during early testing.                                         |
| You need to **document the exact fields** for audit or config files                     | **CoalesceOn[Action]Names**                                 | Explicit, human-readable list that can live outside code.        | Pairs well with admin UI or JSON/YAML configs.                                                         |

`[Action]` can be `Merge`, `Update`, or `Synchronize`, for example:

  * `OnMergeUpdateUseCoalesce`
  * `CoalesceOnUpdateExpression`
  * `CoalesceOnSynchronizeUpdateNames`

## Conclusion

The `Coalesce` option is a simple but powerful safeguard against overwriting existing values with `null` during bulk operations.
Whether you want the behavior to apply to **all properties** (`On[Action]UseCoalesce`), to **specific properties via expressions** (`CoalesceOn[Action]Expression`), or to a **list of property names** (`CoalesceOn[Action]Names`), you can choose the approach that best fits your scenario.

By integrating `Coalesce` into your bulk operations, you can:

* Protect valuable data when the source is incomplete
* Simplify your update logic without writing custom SQL
* Make imports and synchronizations safer and more predictable

When working with data from external systems or partial updates, `Coalesce` ensures that only meaningful values overwrite existing ones — keeping your database clean and consistent.

## Related Articles

### Column Options
- [Input / Output / Ignore](/input-output-ignore)
- [Primary Key](/primary-key)

### Coalesce Options
- [Coalesce](/coalesce)
- [Coalesce Destination](/coalesce-destination)

### Delete Matched Options
- [Delete Matched and Condition](/delete-matched-and-condition)
- [Delete Matched and One NOT Condition](/delete-matched-and-one-not-condition)
- [Delete Matched and Formula](/delete-matched-and-formula)

### Matched Options
- [Matched and Condition](/matched-and-condition)
- [Matched and One NOT Condition](/matched-and-one-not-condition)
- [Matched and Formula](/matched-and-formula)