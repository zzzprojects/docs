---
Title: How to Configure Column Options in Entity Framework Extensions
MetaDescription: Learn how to configure Column Options in Entity Framework Extensions to control which columns are used, include or exclude properties, set custom primary keys, and apply custom SQL formulas for insert, update, and merge operations.
LastMod: 2025-12-17
---

# EF Extensions - Configuring Column Options

In the previous article, we looked at how to [configure options](/configure-options) in **Entity Framework Extensions**.

In this article, we’ll go deeper and explain the basics of **Column Options**, which let you configure things like:

* Which primary key to use
* Which properties to include
* Which properties to exclude
* Whether to use hardcoded SQL

For many options, we offer **three different suffixes**:

* **Expression** — Define the mapping using either a **simple lambda expression** or a **lambda with a body**
* **Names** — Pass a `List<string>` containing property names as strings
* **Formula** — Use hardcoded SQL to take full control over that part of the query

Options with the same name but different suffixes are usually **mutually exclusive**.

For example, you can use either `ColumnPrimaryKeyExpression` **or** `ColumnPrimaryKeyNames`, but not both.

## 📌 Quick Reference — When to Use Each Suffix

| **Suffix**     | **What It Uses**                                 | **Best For**                         | **Example**                                |
| -------------- | ------------------------------------------------ | ------------------------------------ | ------------------------------------------ |
| **Expression** | Lambda expressions referencing entity properties | Strongly-typed mappings in code      | `x => x.ID` or `x => new { x.ID, x.ID2 }`  |
| **Names**      | List of property names as strings                | Dynamic or external property lists   | `new List<string> { nameof(MyEntity.ID) }` |
| **Formula**    | Hardcoded SQL fragments                          | Advanced SQL control (use carefully) | `"NOT DestinationTable.IsLocked"`          |

## Options with the "Expression" Suffix

Options with the **"Expression"** suffix, like `ColumnPrimaryKeyExpression`, let you define the mapping using either:

* A **simple lambda expression** — can only be used when mapping **one property**
* A **lambda with a body** — can be used to map **one or more properties**

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

### Common Options with the "Expression" Suffix

Below is the complete list of options in **Entity Framework Extensions** that use the `"Expression"` suffix:

* **[Primary Key](/primary-key)**

  * `ColumnPrimaryKeyExpression`

* **[Input/Output/Ignore](/input-output-ignore)**

  * `ColumnInputExpression`
  * `ColumnInputOutputExpression`
  * `ColumnOutputExpression`
  * `OnMergeInsertInputExpression`
  * `OnMergeUpdateInputExpression`
  * `OnSynchronizeInsertInputExpression`
  * `OnSynchronizeUpdateInputExpression`
  * `IgnoreColumnOutputExpression`
  * `IgnoreOnInsertExpression`
  * `IgnoreOnUpdateExpression`
  * `IgnoreOnMergeInsertExpression`
  * `IgnoreOnMergeUpdateExpression`
  * `IgnoreOnSynchronizeInsertExpression`
  * `IgnoreOnSynchronizeUpdateExpression`

* **Auto Mapping**

  * `AutoMapIdentityExpression`
  * `AutoMapKeyExpression`

* **[Coalesce](/coalesce)**

  * `CoalesceOnUpdateExpression`
  * `CoalesceOnMergeUpdateExpression`
  * `CoalesceOnSynchronizeUpdateExpression`

* **[Coalesce Destination](/coalesce-destination)**

  * `CoalesceDestinationOnUpdateExpression`
  * `CoalesceDestinationOnMergeUpdateExpression`
  * `CoalesceDestinationOnSynchronizeUpdateExpression`

* **[Matched And Condition](/matched-and-condition)**

  * `UpdateMatchedAndConditionExpression`
  * `DeleteMatchedAndConditionExpression`
  * `MergeMatchedAndConditionExpression`
  * `SynchronizeMatchedAndConditionExpression`
  * `IgnoreOnUpdateMatchedAndConditionExpression`
  * `IgnoreOnDeleteMatchedAndConditionExpression`
  * `IgnoreOnMergeMatchedAndConditionExpression`
  * `IgnoreOnSynchronizeMatchedAndConditionExpression`

* **[Matched And One Not Condition](/matched-and-one-not-condition)**

  * `UpdateMatchedAndOneNotConditionExpression`
  * `DeleteMatchedAndOneNotConditionExpression`
  * `MergeMatchedAndOneNotConditionExpression`
  * `SynchronizeMatchedAndOneNotConditionExpression`
  * `IgnoreOnUpdateMatchedAndOneNotConditionExpression`
  * `IgnoreOnDeleteMatchedAndOneNotConditionExpression`
  * `IgnoreOnMergeMatchedAndOneNotConditionExpression`
  * `IgnoreOnSynchronizeMatchedAndOneNotConditionExpression`

* **Misc**

  * `ColumnAddOrUpdateInputExpression`
  * `ColumnAddOrUpdateInputOutputExpression`
  * `ColumnAddOrUpdateOutputExpression`
  * `ColumnStagingTableFormulaExpression`
  * `ColumnSynchronizeDeleteKeySubsetExpression`
  * `QueryFilterPrimaryKeyExpression`

## Options with the "Names" Suffix

Options with the **"Names"** suffix, like `ColumnPrimaryKeyNames`, let you create a `List<string>` and pass property names as strings.

We always **recommend** using the `nameof` operator whenever possible. This helps prevent typos and avoids breaking your code during refactoring.

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

### Common Options with the "Names" Suffix

Below is the complete list of options in **Entity Framework Extensions** that use the `"Names"` suffix:

* **[Primary Key](/primary-key)**

  * `ColumnPrimaryKeyNames`

* **[Input/Output/Ignore](/input-output-ignore)**

  * `ColumnInputNames`
  * `ColumnInputOutputNames`
  * `ColumnOutputNames`
  * `OnMergeInsertInputNames`
  * `OnMergeUpdateInputNames`
  * `OnSynchronizeInsertInputNames`
  * `OnSynchronizeUpdateInputNames`
  * `IgnoreOnInsertNames`
  * `IgnoreColumnOutputNames`
  * `IgnoreOnUpdateNames`
  * `IgnoreOnMergeInsertNames`
  * `IgnoreOnMergeUpdateNames`
  * `IgnoreOnSynchronizeInsertNames`
  * `IgnoreOnSynchronizeUpdateNames`

* **Auto Mapping**

  * `AutoMapIdentityName` *(Note: this property is a `string`)*
  * `AutoMapKeyName` *(Note: this property is a `string`)*

* **[Coalesce](/coalesce)**

  * `CoalesceOnUpdateNames`
  * `CoalesceOnMergeUpdateNames`
  * `CoalesceOnSynchronizeUpdateNames`

* **[Coalesce Destination](/coalesce-destination)**

  * `CoalesceDestinationOnUpdateNames`
  * `CoalesceDestinationOnMergeUpdateNames`
  * `CoalesceDestinationOnSynchronizeUpdateNames`

* **[Matched And Condition](/matched-and-condition)**

  * `UpdateMatchedAndConditionNames`
  * `DeleteMatchedAndConditionNames`
  * `MergeMatchedAndConditionNames`
  * `SynchronizeMatchedAndConditionNames`
  * `IgnoreOnUpdateMatchedAndConditionNames`
  * `IgnoreOnDeleteMatchedAndConditionNames`
  * `IgnoreOnMergeMatchedAndConditionNames`
  * `IgnoreOnSynchronizeMatchedAndConditionNames`

* **[Matched And One Not Condition](/matched-and-one-not-condition)**

  * `UpdateMatchedAndOneNotConditionNames`
  * `DeleteMatchedAndOneNotConditionNames`
  * `MergeMatchedAndOneNotConditionNames`
  * `SynchronizeMatchedAndOneNotConditionNames`
  * `IgnoreOnUpdateMatchedAndOneNotConditionNames`
  * `IgnoreOnDeleteMatchedAndOneNotConditionNames`
  * `IgnoreOnMergeMatchedAndOneNotConditionNames`
  * `IgnoreOnSynchronizeMatchedAndOneNotConditionNames`

* **Misc**

  * `ColumnAddOrUpdateInputNames`
  * `ColumnAddOrUpdateInputOutputNames`
  * `ColumnAddOrUpdateOutputNames`
  * `ColumnStagingTableFormulaNames`
  * `ColumnSynchronizeDeleteKeySubsetNames`
  * `QueryFilterPrimaryKeyNames`

## Options with the "Formula" Suffix

Options with the **"Formula"** suffix, like `MergeMatchedAndFormula`, let you hardcode a SQL fragment that is inserted directly into the generated SQL statement.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

// Use a formula to avoid updating "locked" rows
context.BulkMerge(list, options =>
{
    options.MergeMatchedAndFormula = "NOT DestinationTable.IsLocked";
});
```

⚠️ **Security Warning:**
Never use hardcoded SQL that comes from **user input**.
You should always have **full control** over any SQL written in a formula to prevent SQL injection.

### Common Options with the "Formula" Suffix

Below is the complete list of options in **Entity Framework Extensions** that use the `"Formula"` suffix:

* **[Primary Key](/primary-key)**

  * `InsertPrimaryKeyAndFormula`
  * `UpdatePrimaryKeyAndFormula`
  * `DeletePrimaryKeyAndFormula`
  * `MergePrimaryKeyAndFormula`

* **Staging Table Filter**

  * `InsertStagingTableFilterFormula`
  * `UpdateStagingTableFilterFormula`
  * `DeleteStagingTableFilterFormula`
  * `MergeStagingTableFilterFormula`

* **[Matched And Formula](/matched-and-formula)**

  * `UpdateMatchedAndFormula`
  * `DeleteMatchedAndFormula`
  * `MergeMatchedAndFormula`
  * `SynchronizeMatchedAndFormula`

* **Not Matched And Formula**

  * `InsertNotMatchedAndFormula`
  * `MergeNotMatchedAndFormula`
  * `SynchronizeNotMatchedAndFormula`

* **Misc**

  * `ColumnSynchronizeDeleteKeySubsetFormula`
  * `SynchronizeDeleteDestinationTableFilterFormula`
  * `SynchronizeSoftDeleteFormula`
  * `QueryFilterPrimaryKeyAndFormula`

## Summary

Column Options give you **full control** over how data is saved with Entity Framework Extensions.

By choosing between the **Expression**, **Names**, and **Formula** suffixes, you can decide exactly:

* Which columns are used
* How properties are mapped
* When custom SQL logic is required

In most cases:

* **Expression** is the best choice when working directly with entity properties in code
* **Names** is useful when property names must come from dynamic or external sources
* **Formula** gives you the most control by injecting SQL, but should always be used carefully

With Column Options, you are no longer limited to the default `SaveChanges` behavior.

You can adapt your [insert](/bulk-insert), [update](/bulk-update), and [merge](/bulk-merge) operations to match your exact business rules and performance needs.