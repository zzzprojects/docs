---
Title: EF Core Bulk Insert Optimized with Entity Framework Extensions
MetaDescription: Boost your EF Core insert performance using the BulkInsertOptimized method from Entity Framework Extensions. Quickly insert large volumes of entities without returning values—perfect for maximum speed. Get smart hints and recommendations to further improve your insert performance. Try it now.
LastMod: 2025-08-03
---

# Bulk Insert Optimized with Entity Framework Extensions /n Instantly maximize your insert performance in EF Core 9

The `BulkInsertOptimized` method is a unique feature from Entity Framework Extensions. It’s the **fastest** way to insert entities in EF Core.

By default, it uses the most efficient SQL because it doesn’t return any values—unless you explicitly ask for them. This allows it to skip the extra steps used by our [BulkInsert](/bulk-insert) method, making it even faster.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

// Easy to use
context.BulkInsertOptimized(customers);

// Easy to customize
context.BulkInsertOptimized(invoices, options => options.IncludeGraph = true);
```

[Online Example (EF Core)](https://dotnetfiddle.net/DEgyZF)

### What advantages does `BulkInsertOptimized` have over `BulkInsert`?

* **No output values by default**
  Since it doesn't return values, it can write directly to the destination table using the `BulkCopy` strategy—no need for temporary tables. That means one less step and much better performance.

* **Smart performance tips**
  It can also give you helpful suggestions and best practices to make your insert operations as fast and efficient as possible.


## What Are the Performance Gains When Not Outputting Values?

Whether you choose to output values or not when using bulk insert with Entity Framework Extensions, you'll still get **major performance improvements**.

However, when performance really matters, **every millisecond counts**—and skipping output values gives you an extra boost.

Below is a benchmark showing the performance for inserting a list of `Customer` entities, where `CustomerID` is an identity column:

| Operation                          | 1,000 Entities | 2,000 Entities | 5,000 Entities |
| :--------------------------------- | -------------: | -------------: | -------------: |
| SaveChanges                        |         325 ms |         575 ms |       1,400 ms |
| BulkInsert (with output values)    |          60 ms |          90 ms |         150 ms |
| BulkInsert (without output values) |          30 ms |          50 ms |          90 ms |
| BulkInsertOptimized                |          30 ms |          50 ms |          90 ms |

👉 [Try our Online Benchmark](https://dotnetfiddle.net/RfxOjO)

## BulkInsertOptimized Recommendations and Performance Hints

The `BulkInsertOptimized` method from Entity Framework Extensions returns an instance of the `BulkOptimizedAnalysis` class.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

public class BulkOptimizedAnalysis 
{
    /// <summary>True if the bulk insert is optimized.</summary>
    public bool IsOptimized { get; }

    /// <summary>Text summary with all tips to optimize the bulk insert method.</summary>
    public string TipsText { get; }
    
    /// <summary>List of individual tips to optimize the bulk insert method.</summary>
    public List<string> Tips { get; }
}
```

In short:

* **IsOptimized** — returns `true` if the bulk insert operation is fully optimized.
* **Tips** — returns a list of reasons why the insert is **not** optimized.
* **TipsText** — gives the same tips but combined into a single string for easy logging or display.

To better understand how this class works, take a look at this [online example](https://dotnetfiddle.net/FZJSnE):

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

// Example 1
var analysis = context.BulkInsertOptimized(customers);

// Example 2
var analysis = context.BulkInsertOptimized(customers, options => {
    options.InsertIfNotExists = true;
});
```

* **Example 1** is optimized. For SQL Server, a direct `SqlBulkCopy` is used—simple and fast.
* **Example 2** is **not optimized**. The `InsertIfNotExists = true` option can't be handled directly by `SqlBulkCopy`, so it requires extra logic with a temporary table, which slows things down.

The returned tip would be:

> "The option `InsertIfNotExists = true` forces the use of a less efficient strategy, resulting in a considerable performance penalty."

## 🔍 What is Supported?

The `BulkInsertOptimized` method from **Entity Framework Extensions** supports all the common scenarios in EF Core — and nearly everything you can do with EF Core and EF6!

* ✅ The latest EF Core version: **EF Core 9**
* ✅ All previous EF Core versions: **EF Core 2 to 8**
* ✅ All Entity Framework versions: **EF6, EF5, EF4, and EF Classic**
* ✅ All major database providers: **SQL Server, SQL Azure, PostgreSQL, MySQL, MariaDB, SQLite, and Oracle**
* ✅ All inheritance strategies: **TPC, TPH, and TPT**
* ✅ **Complex types** / **Owned entity types**
* ✅ **Enums**
* ✅ **Value converters** (EF Core)
* ✅ And much more — even **shadow properties**!

## Bulk Insert Optimized Options

Please refer to the [Bulk Insert Options](/bulk-insert#bulk-insert-options) documentation

## Troubleshooting

Please refer to the [Bulk Insert - Troubleshooting](/bulk-insert#troubleshooting) documentation

## Limitations

Please refer to the [Bulk Insert - Limitations](/bulk-insert#limitations) documentation

## Conclusion

The `BulkInsertOptimized` method from Entity Framework Extensions is the fastest and most efficient ways to insert large volumes of data with EF Core.

Unlike the standard [`BulkInsert`](/bulk-insert) method, it skips returning output values by default, allowing it to use a direct `SqlBulkCopy` strategy and avoid unnecessary steps like creating temporary tables.

But what truly sets it apart is the built-in `BulkOptimizedAnalysis` class. This feature doesn’t just perform well — it **tells you** how well it performs, and **why**. You’ll know right away if your insert is fully optimized, and what to change if it’s not.

If maximum speed matters in your application — and you’re not relying on database-generated output values — then `BulkInsertOptimized` is the tool you’ve been looking for.

👉 [Try it now](https://entityframework-extensions.net/download) and experience the performance difference.
