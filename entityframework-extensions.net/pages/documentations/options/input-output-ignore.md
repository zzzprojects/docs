---
**Title:** Input, Output, and Ignore Column Options in Entity Framework Extensions
**MetaDescription:** Learn how to use Input, Output, and Ignore column options in Entity Framework Extensions to control which properties are inserted, updated, or returned. Improve performance, prevent unwanted updates, and keep your data clean.
**LastMod:** 2025-08-12
---

# Input / Output / Ignore

In this article, we will discover how to choose which properties are used when inserting, updating, or deleting data through **Entity Framework Extensions**.

By default, all mapped properties are used — but this is not always the most efficient choice. Sometimes, you may want to update only specific properties, such as `FirstName` and `LastName`, or skip properties that should not be updated, such as `CreatedDate`.

Selecting only the properties you need can **reduce the amount of data sent to the database** and **prevent accidental overwrites of important fields**.

There are usually two ways to control this:

* **Choose which properties to map** — all other properties will be ignored.
* **Choose which properties to ignore** — all other properties will be included.

Both approaches can be combined. For example:

* Map a few properties using `ColumnInputExpression`.
* Ignore specific properties for the insert operation using `IgnoreOnInsertExpression`.

---

## Prerequisites

Before continuing, we recommend reading the following articles first:

* [Configure Options](/configure-options)
* [Configure Column Options](/configure-column-options)

---

## Quick Reference Table

| Category                   | Option Types                                                                                                                                     | Purpose                                                                | Typical Usage                                                     |
| -------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------ | ---------------------------------------------------------------------- | ----------------------------------------------------------------- |
| **Column Input**           | `ColumnInputExpression`, `ColumnInputNames`                                                                                                      | Choose which columns are sent to the database.                         | Update only `FirstName` and `LastName`, skip audit fields.        |
| **Column Output**          | `ColumnOutputExpression`, `ColumnOutputNames`                                                                                                    | Choose which columns are returned after the operation.                 | Fetch only `ModifiedDate` updated by a trigger.                   |
| **Column InputOutput**     | `ColumnInputOutputExpression`, `ColumnInputOutputNames`                                                                                          | Send and receive values for the same columns.                          | Send a value for processing and get back the recalculated result. |
| **OnMerge**                | `OnMergeInsertInputExpression`, `OnMergeUpdateInputExpression`, `OnMergeInsertInputNames`, `OnMergeUpdateInputNames`                             | Map input columns separately for insert and update during merge.       | Insert all columns, update only a few fields.                     |
| **OnSynchronize**          | `OnSynchronizeInsertInputExpression`, `OnSynchronizeUpdateInputExpression`, `OnSynchronizeInsertInputNames`, `OnSynchronizeUpdateInputNames`     | Map input columns separately for insert and update during synchronize. | Insert all columns, update only modified ones.                    |
| **Ignore (All)**           | `IgnoreColumnOutputExpression`, `IgnoreColumnOutputNames`                                                                                        | Skip specific columns entirely from output mapping.                    | Avoid fetching large blobs unless needed.                         |
| **Ignore on Insert**       | `IgnoreOnInsertExpression`, `IgnoreOnInsertNames`                                                                                                | Skip specific columns during insert.                                   | Ignore `ModifiedDate` because no modification yet.                |
| **Ignore on Update**       | `IgnoreOnUpdateExpression`, `IgnoreOnUpdateNames`                                                                                                | Skip specific columns during update.                                   | Ignore `CreatedDate` to preserve original value.                  |
| **Ignore for Merge**       | `IgnoreOnMergeInsertExpression`, `IgnoreOnMergeUpdateExpression`, `IgnoreOnMergeInsertNames`, `IgnoreOnMergeUpdateNames`                         | Skip specific columns separately for insert/update in merge.           | Insert: skip `ModifiedDate`; Update: skip `CreatedDate`.          |
| **Ignore for Synchronize** | `IgnoreOnSynchronizeInsertExpression`, `IgnoreOnSynchronizeUpdateExpression`, `IgnoreOnSynchronizeInsertNames`, `IgnoreOnSynchronizeUpdateNames` | Skip specific columns separately for insert/update in synchronize.     | Insert: skip `ModifiedDate`; Update: skip `CreatedDate`.          |

---

## Column Input

The `ColumnInputExpression` and `ColumnInputNames` options in **Entity Framework Extensions** let you specify which properties will be inserted or updated — all other properties will be ignored.

These options apply to the following bulk operations:

* [BulkInsert](/bulk-insert)
* [BulkInsertOptimized](/bulk-insert-optimized)
* [BulkUpdate](/bulk-update)
* [BulkMerge](/bulk-merge)
* [BulkSynchronize](/bulk-synchronize)

Restricting the **Input** columns to only what’s necessary reduces unnecessary database writes and helps prevent accidental changes to other fields.

For example, you might update only `FirstName` and `LastName` without touching audit fields such as `CreatedDate`.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

// ColumnInputExpression: Select properties to insert or update
context.BulkUpdate(customers, options => { 
    options.ColumnInputExpression = x => new { x.FirstName, x.LastName };
});

// ColumnInputNames: Select properties by name
context.BulkUpdate(customers, options => {
    options.ColumnInputNames = new List<string>() { nameof(Customer.FirstName), nameof(Customer.LastName) };
});
```

---

## Column Output

The `ColumnOutputExpression` and `ColumnOutputNames` options in **Entity Framework Extensions** let you choose which columns will be returned after an insert or update.

The values from the database are automatically mapped back to your entities.

These options apply to the following bulk operations:

* [BulkInsert](/bulk-insert)
* [BulkInsertOptimized](/bulk-insert-optimized)
* [BulkUpdate](/bulk-update)
* [BulkMerge](/bulk-merge)
* [BulkSynchronize](/bulk-synchronize)

Restricting the **Output** columns avoids fetching unused data, which helps improve performance in high-throughput scenarios.

For example, if you have a trigger that automatically updates the `ModifiedDate` column, you can return only that column so the new value is reflected in your entities without reloading the entire row.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

// ColumnOutputExpression: Return only the updated ModifiedDate
context.BulkUpdate(customers, options => { 
    options.ColumnInputExpression = x => new { x.FirstName, x.LastName };
    options.ColumnOutputExpression = x => new { x.ModifiedDate };
});

// ColumnOutputNames: Return only the updated ModifiedDate by name
context.BulkUpdate(customers, options => {
    options.ColumnInputExpression = x => new { x.FirstName, x.LastName };
    options.ColumnOutputNames = new List<string>() { nameof(Customer.ModifiedDate) };
});
```

---

## Column InputOutput

The `ColumnInputOutputExpression` and `ColumnInputOutputNames` options in **Entity Framework Extensions** combine the functionality of both **Column Input** and **Column Output**. They work in both directions:

* **Input:** The value is taken from the entity and sent to the database for the insert, update, or merge.
* **Output:** The updated value from the database is mapped back to the entity after the operation.

These options apply to the following bulk operations:

* [BulkInsert](/bulk-insert)
* [BulkInsertOptimized](/bulk-insert-optimized)
* [BulkUpdate](/bulk-update)
* [BulkMerge](/bulk-merge)
* [BulkSynchronize](/bulk-synchronize)

Restricting **InputOutput** columns avoids sending and fetching unused data, which improves performance and prevents accidental overwrites.

This mapping is especially useful if you have database-side logic (such as triggers) that modifies values during the operation.

For example, you might send a value to the database for processing, and then receive back a normalized or recalculated version of it.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

// ColumnInputOutputExpression: return ModifiedDate
context.BulkMerge(customers, options => { 
    options.ColumnInputExpression = x => new { x.FirstName, x.LastName };
    options.ColumnInputOutputExpression = x => new { x.ModifiedDate };
});

// ColumnInputOutputNames: return ModifiedDate by name
context.BulkMerge(customers, options => {
    options.ColumnInputNames = new List<string>() { nameof(Customer.FirstName), nameof(Customer.LastName) };
    options.ColumnInputOutputNames = new List<string>() { nameof(Customer.ModifiedDate) };
});
```

---

## OnMerge / OnSynchronize

The [BulkMerge](/bulk-merge) and [BulkSynchronize](/bulk-synchronize) methods handle **both insert and update** behaviors.

**Note:** Depending on the database provider, this can run as a single upsert-style statement or as multiple statements/batches.

* Some providers support a true single-statement upsert.
* Others emulate the same result using a sequence of insert/update steps.

Sometimes, you may want to map columns for **only one** of these actions — for example, insert all columns but update only a few. This helps avoid sending unused data, improves performance, and reduces the risk of overwriting fields that should not change.

You can control this per action with the following options:

* **BulkMerge**

  * `OnMergeInsertInputExpression`
  * `OnMergeUpdateInputExpression`
  * `OnMergeInsertInputNames`
  * `OnMergeUpdateInputNames`

* **BulkSynchronize**

  * `OnSynchronizeInsertInputExpression`
  * `OnSynchronizeUpdateInputExpression`
  * `OnSynchronizeInsertInputNames`
  * `OnSynchronizeUpdateInputNames`

You can also mix these with `ColumnInputExpression` or `ColumnInputNames`.

For example, define a default set of input columns, then further restrict the **update** part using `OnMergeUpdateInputExpression`.

### Bulk Merge example

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

// Expressions: Insert CreatedDate, Update ModifiedDate
context.BulkMerge(customers, options => { 
    options.ColumnInputExpression = x => new { x.FirstName, x.LastName };

    // Per-action input overrides
    options.OnMergeInsertInputExpression = x => new { x.CreatedDate };
    options.OnMergeUpdateInputExpression = x => new { x.ModifiedDate };
});

// Names: Insert CreatedDate, Update ModifiedDate
context.BulkMerge(customers, options => {
    options.ColumnInputNames = new List<string> { nameof(Customer.FirstName), nameof(Customer.LastName) };

    // Per-action input overrides
    options.OnMergeInsertInputNames = new List<string> { nameof(Customer.CreatedDate) };
    options.OnMergeUpdateInputNames = new List<string> { nameof(Customer.ModifiedDate) };
});
```

### Bulk Synchronize example

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

// Expressions: Insert CreatedDate, Update ModifiedDate
context.BulkSynchronize(customers, options => { 
    options.ColumnInputExpression = x => new { x.FirstName, x.LastName };

    options.OnSynchronizeInsertInputExpression = x => new { x.CreatedDate };
    options.OnSynchronizeUpdateInputExpression = x => new { x.ModifiedDate };
});

// Names: Insert CreatedDate, Update ModifiedDate
context.BulkSynchronize(customers, options => {
    options.ColumnInputNames = new List<string> { nameof(Customer.FirstName), nameof(Customer.LastName) };

    options.OnSynchronizeInsertInputNames = new List<string> { nameof(Customer.CreatedDate) };
    options.OnSynchronizeUpdateInputNames = new List<string> { nameof(Customer.ModifiedDate) };
});
```

### Bulk Synchronize example

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

// Expressions: Insert CreatedDate, Update ModifiedDate
context.BulkSynchronize(customers, options => { 
    options.ColumnInputExpression = x => new { x.FirstName, x.LastName };

    options.OnSynchronizeInsertInputExpression = x => new { x.CreatedDate };
    options.OnSynchronizeUpdateInputExpression = x => new { x.ModifiedDate };
});

// Names: Insert CreatedDate, Update ModifiedDate
context.BulkSynchronize(customers, options => {
    options.ColumnInputNames = new List<string> { nameof(Customer.FirstName), nameof(Customer.LastName) };

    options.OnSynchronizeInsertInputNames = new List<string> { nameof(Customer.CreatedDate) };
    options.OnSynchronizeUpdateInputNames = new List<string> { nameof(Customer.ModifiedDate) };
});
```

---

## Ignore

Instead of selecting which columns to include, you can **ignore** specific columns.

Ignoring columns helps you avoid sending or returning data you don’t need, which improves performance and reduces the risk of overwriting fields that should remain unchanged.

A common best practice:

* **On insert** → Ignore `ModifiedDate` because there’s no modification yet.
* **On update** → Ignore `CreatedDate` to preserve the original creation date.

These options are available:

* **All**

  * `IgnoreColumnOutputExpression`
  * `IgnoreColumnOutputNames`

* **Bulk Insert**

  * `IgnoreOnInsertExpression`
  * `IgnoreOnInsertNames`

* **Bulk Update**

  * `IgnoreOnUpdateExpression`
  * `IgnoreOnUpdateNames`

* **Bulk Merge**

  * `IgnoreOnMergeInsertExpression`
  * `IgnoreOnMergeUpdateExpression`
  * `IgnoreOnMergeInsertNames`
  * `IgnoreOnMergeUpdateNames`

* **Bulk Synchronize**

  * `IgnoreOnSynchronizeInsertExpression`
  * `IgnoreOnSynchronizeUpdateExpression`
  * `IgnoreOnSynchronizeInsertNames`
  * `IgnoreOnSynchronizeUpdateNames`

---

### Ignore Output Columns (example)

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

// Expression: do not fetch LargeImage after the operation (saves bandwidth)
context.BulkUpdate(customers, options =>
{
    options.IgnoreColumnOutputExpression = x => new { x.LargeImage };
});

// Names: do not fetch LargeImage after the operation
context.BulkUpdate(customers, options =>
{
    options.IgnoreColumnOutputNames = new List<string> { nameof(Customer.LargeImage) };
});
```

---

### Ignore on Insert (example)

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

// Expression: do not write ModifiedDate during insert
context.BulkInsert(customers, options =>
{
    options.IgnoreOnInsertExpression = x => new { x.ModifiedDate };
});

// Names: do not write ModifiedDate during insert
context.BulkInsert(customers, options =>
{
    options.IgnoreOnInsertNames = new List<string> { nameof(Customer.ModifiedDate) };
});
```

---

### Ignore on Update (example)

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

// Expression: do not update CreatedDate during update (preserve original creation date)
context.BulkUpdate(customers, options =>
{
    options.IgnoreOnUpdateExpression = x => new { x.CreatedDate };
});

// Names: do not update CreatedDate during update (preserve original creation date)
context.BulkUpdate(customers, options =>
{
    options.IgnoreOnUpdateNames = new List<string> { nameof(Customer.CreatedDate) };
});
```

---

### Ignore for Merge (example)

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

// Expression: ignore specific columns per action in BulkMerge
context.BulkMerge(customers, options =>
{
    // On insert, do not write ModifiedDate
    options.IgnoreOnMergeInsertExpression = x => new { x.ModifiedDate };

    // On update, do not touch CreatedDate (preserve original creation date)
    options.IgnoreOnMergeUpdateExpression = x => new { x.CreatedDate };
});

// Names: ignore specific columns per action in BulkMerge
context.BulkMerge(customers, options =>
{
    // On insert, do not write ModifiedDate
    options.IgnoreOnMergeInsertNames = new List<string> { nameof(Customer.ModifiedDate) };

    // On update, do not touch CreatedDate (preserve original creation date)
    options.IgnoreOnMergeUpdateNames = new List<string> { nameof(Customer.CreatedDate) };
});
```

---

### Ignore for Synchronize (example)

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

// Expression: ignore specific columns per action in BulkSynchronize
context.BulkSynchronize(customers, options =>
{
    // On insert, do not write ModifiedDate
    options.IgnoreOnSynchronizeInsertExpression = x => new { x.ModifiedDate };

    // On update, do not touch CreatedDate (preserve original creation date)
    options.IgnoreOnSynchronizeUpdateExpression = x => new { x.CreatedDate };
});

// Names: ignore specific columns per action in BulkSynchronize
context.BulkSynchronize(customers, options =>
{
    // On insert, do not write ModifiedDate
    options.IgnoreOnSynchronizeInsertNames = new List<string> { nameof(Customer.ModifiedDate) };

    // On update, do not touch CreatedDate (preserve original creation date)
    options.IgnoreOnSynchronizeUpdateNames = new List<string> { nameof(Customer.CreatedDate) };
});
```

## Conclusion

Mastering the **Input**, **Output**, and **Ignore** column options in **Entity Framework Extensions** gives you fine-grained control over how data is sent to and retrieved from your database. By mapping only the properties you need—or ignoring those you don’t—you can:

* **Improve performance** by reducing the amount of data processed.
* **Protect critical fields** like `CreatedDate` and `ModifiedDate` from accidental changes.
* **Work efficiently with triggers and computed columns** without unnecessary round-trips or overwrites.

Whether you’re inserting, updating, merging, or synchronizing data, these options let you adapt bulk operations to your exact needs while keeping your database clean, fast, and reliable. Start with small adjustments, test your mappings, and you’ll quickly see the benefits in both speed and data integrity.
