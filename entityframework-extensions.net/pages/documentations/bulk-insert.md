---
Title: Bulk Insert in EF Core with Entity Framework Extensions
MetaDescription: Boost your EF Core inserts performance by up to 15x, reducing insert time by 94% with Bulk Insert from Entity Framework Extensions. Use BulkInsert method in EF Core to handle thousands of entities with less memory and more control. Fully supports all EF Core and EF6 versions. Try the live benchmark now!
LastMod: 2026-05-26
---

# EF Core Bulk Insert with Entity Framework Extensions

The `BulkInsert` method from Entity Framework Extensions is the most flexible way to insert entities in EF Core and EF6. It allows you to insert thousands of entities quickly while giving you full control over how the operation behaves.

You can customize the insert with various [options](/bulk-insert#bulk-insert-options), such as preserving identity values, inserting only new entities, including related entities, and much more.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

// Easy to use
await context.BulkInsertAsync(customers);

// Easy to customize
await context.BulkInsertAsync(invoices, options => options.IncludeGraph = true);
```

[Online Example](https://dotnetfiddle.net/2eVfFT)

If your scenario does not require returning generated values such as identity keys, you can use [BulkInsertOptimized](/bulk-insert-optimized) for even better performance.

Here's the key difference between both methods:

* **BulkInsert:** `AutoMapOutputDirection = true` by default. Returns generated values such as identity keys, but may generate slightly less optimized SQL.
* **BulkInsertOptimized:** `AutoMapOutputDirection = false` by default. Skips returning generated values for maximum performance unless explicitly enabled.

## Bulk Insert Example

### Insert If Not Exists

Use the `InsertIfNotExists` option when you want to insert entities only if they do not already exist in the destination table.

By default, existing rows are matched using your entity's mapped primary key. You can also use the [ColumnPrimaryKeyExpression](/primary-key#using-columnprimarykeyexpression) and [ColumnPrimaryKeyNames](/primary-key#using-columnprimarykeynames) options to define a custom key (or a combination of properties) used to determine whether a row already exists.

This is useful when importing data and you want to avoid inserting duplicate records.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

// Using mapped primary key
context.BulkInsert(customers, options => options.InsertIfNotExists = true);

// Using a custom key
context.BulkInsert(customers, options => {
    options.InsertIfNotExists = true;
    options.ColumnPrimaryKeyExpression = x => new { x.Code };
});
```

[Online Example](https://dotnetfiddle.net/q7dFoR)

### Insert with Identity Value

By default, when inserting new rows, the database generates the value for identity columns. Use the `InsertKeepIdentity` option when you want to preserve the identity value from your entities instead.

This is useful when importing existing data where identity values must be preserved.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

context.BulkInsert(customers, options => options.InsertKeepIdentity = true);
```

[Online Example](https://dotnetfiddle.net/fUwLGI)

### Insert with Related Entities (Include Graph)

Use this option when you want to insert entities and automatically insert all related entities (children, grandchildren, and more) linked through navigation properties.

* [IncludeGraph](/include-graph): Automatically inserts all related entities linked through navigation properties.
* [IncludeGraphOperationBuilder](/include-graph#includegraphoperationbuilder): Lets you customize how a specific entity type is inserted.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

context.BulkInsert(invoices, options => options.IncludeGraph = true);
```

[Online Example](https://dotnetfiddle.net/8JHVhN)

### Insert Without Outputting Values

By default, `BulkInsert` returns generated values such as identity keys and maps them back to your entities. If you don't need those values, you can disable this behavior for better performance.

Use the `AutoMapOutputDirection` option to skip outputting generated values.

If you never need output values and want the best possible performance, consider using [BulkInsertOptimized](/bulk-insert-optimized), which is specifically designed for that scenario.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

context.BulkInsert(customers, options => options.AutoMapOutputDirection = false);
```

[Online Example](https://dotnetfiddle.net/f0zuL5)

### Insert with Rows Affected

Use the [UseRowsAffected](/rows-affected) option to retrieve the number of rows affected by the `BulkInsert` operation.

This is useful when you need to verify how many rows were actually inserted for logging, validation, or reporting.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

var resultInfo = new Z.BulkOperations.ResultInfo();

context.BulkInsert(customers, options =>
{
    options.UseRowsAffected = true;
    options.ResultInfo = resultInfo;
});

int rowsAffected = resultInfo.RowsAffected;
int rowsAffectedInserted = resultInfo.RowsAffectedInserted;
```

[Online Example](https://dotnetfiddle.net/oPM8wl)

### More Examples

Need a scenario not covered here?

There’s a good chance we already support it.

**[Contact us to discuss your scenario](/contact-us)**

## 🔑 Key Benefits

One of the main reasons developers choose **EF Core Bulk Insert** from **Entity Framework Extensions** is the **performance** and **low memory usage**. Another big reason? Its **flexibility** — with **hundreds of supported options** (we’ll explore some of them later).

* ✅ **Extremely fast inserts** — Insert millions of rows in seconds instead of minutes.
* ✅ **Memory-efficient** — Keep memory usage low, even when working with huge datasets.
* ✅ **Highly flexible** — Customize the behavior with hundreds of options to match your exact needs.

## 🔍 What is Supported?

The EF6 / EF Core BulkInsert method from **Entity Framework Extensions** supports all the common scenarios in EF Core — and nearly everything you can do with EF Core and EF6!

* ✅ The latest EF Core version: **EF Core 10**
* ✅ All previous EF Core versions: **EF Core 2 to 9**
* ✅ All Entity Framework versions: **EF6, EF5, EF4, and EF Classic**
* ✅ All major database providers: **SQL Server, SQL Azure, PostgreSQL, MySQL, MariaDB, SQLite, and Oracle**
* ✅ All inheritance strategies: **TPC, TPH, and TPT**
* ✅ **Complex types** / **Owned entity types**
* ✅ **Enums**
* ✅ **Value converters** (EF Core)
* ✅ And much more — even **shadow properties**!

## 🚀 Performance Comparison (SaveChanges vs Bulk Insert)

A very popular search on Google is **"Fastest way to Bulk Insert in EF Core"**—and that’s exactly what our `BulkInsert` method in Entity Framework Extensions delivers!

Don't just take our word for it or blindly trust what we say. Instead, try it yourself using our online benchmark and see the results with a single click!

### 📊 Benchmark Results

The tables above give you a quick idea of the performance gains when using **Bulk Insert in EF Core**.  
But to give you a more complete picture, we also ran **extensive benchmarks across all major database providers** with BenchmarkDotNet.

👉 Explore detailed results:  

- By provider (EF Core):
   - [SQL Server](https://github.com/zzzprojects/EntityFramework-Extensions/blob/master/benchmark-result/efcore-sqlserver.md)
   - [PostgreSQL](https://github.com/zzzprojects/EntityFramework-Extensions/blob/master/benchmark-result/efcore-postgresql.md)
   - [MySQL](https://github.com/zzzprojects/EntityFramework-Extensions/blob/master/benchmark-result/efcore-mysql.md)
   - [MariaDB](https://github.com/zzzprojects/EntityFramework-Extensions/blob/master/benchmark-result/efcore-mariadb.md)
   - [Oracle](https://github.com/zzzprojects/EntityFramework-Extensions/blob/master/benchmark-result/efcore-oracle.md)
   - [SQLite](https://github.com/zzzprojects/EntityFramework-Extensions/blob/master/benchmark-result/efcore-sqlite.md)  
- By operation (EF Core):
   - [Bulk Insert](https://github.com/zzzprojects/EntityFramework-Extensions/blob/master/benchmark-result/efcore-bulk-insert.md)
   - [Bulk Update](https://github.com/zzzprojects/EntityFramework-Extensions/blob/master/benchmark-result/efcore-bulk-update.md)
   - [Bulk Delete](https://github.com/zzzprojects/EntityFramework-Extensions/blob/master/benchmark-result/efcore-bulk-delete.md)
   - [Bulk Merge](https://github.com/zzzprojects/EntityFramework-Extensions/blob/master/benchmark-result/efcore-bulk-merge.md)
   - [Bulk SaveChanges](https://github.com/zzzprojects/EntityFramework-Extensions/blob/master/benchmark-result/efcore-bulk-savechanges.md)
   - [Bulk Synchronize](https://github.com/zzzprojects/EntityFramework-Extensions/blob/master/benchmark-result/efcore-bulk-synchronize.md)  

Here’s an example chart for **SQL Server – Bulk Insert** comparing **EF Core SaveChanges** vs **EF Extensions BulkInsert**:

![Benchmark EF Core vs EF Extensions – SQL Server - Bulk Insert](https://raw.githubusercontent.com/zzzprojects/EntityFramework-Extensions/master/images/benchmark-efcore-vs-efe-sqlserver-bulk-insert.png)


### EF Core vs Entity Framework Extensions

The `SaveChanges` method in EF Core is much faster than it was back in the EF6 days when inserting data. Why the improvement? The EF Core team introduced a new approach using a `MERGE` statement with a `RETURNING` clause—similar to the one we’ve been using in Entity Framework Extensions for SQL Server since 2014. So yes, we were already doing something right!

Even with this new strategy, our Entity Framework Extensions library is still faster. That’s because we use `SqlBulkCopy` and have deeply optimized how data is handled behind the scenes. In fact, the performance gap becomes even more noticeable when your entities have more than just a few properties. If your entity only has 2 or 3 properties—like a very simple `Customer` with just `Id`, `Name`, and `Email`—the difference might seem smaller. But once you deal with real-world entities containing 10, 15, or more properties, **our optimized approach truly shines**.


| Operation                           | 1,000 Entities | 2,000 Entities | 5,000 Entities |
| :---------------------------------- | -------------: | -------------: | -------------: |
| SaveChanges                         | 325 ms         | 575 ms         | 1,400 ms       |
| BulkInsert (Outputting values)      | 60 ms          | 90 ms          | 150 ms         |
| BulkInsert (Not Outputting values)  | 30 ms          | 50 ms          | 90 ms          |
| BulkInsertOptimized                 | 30 ms          | 50 ms          | 90 ms          |

👉 [Try our Online Benchmark](https://dotnetfiddle.net/ttbri7)

In other words, to save 5,000 entities:
- **BulkInsert (Outputting values)** is about **9x faster**, reducing insert time by **89%**.
- **BulkInsert (Not Outputting values)** is about **15x faster**, reducing insert time by **94%**.

Our Entity Framework Extensions library provides the best performance when no data needs to be returned/outputted. That’s why we introduced the `AutoMapOutputDirection = false` option and the [BulkInsertOptimized](/bulk-insert-optimized) method.

### EF Core vs Entity Framework Extensions + Include Graph

Another important benchmark for EF Core is when inserting data that includes a graph of related entities. Being faster is one big advantage we offer—but just as important, our Entity Framework Extensions library uses only a **fraction of the memory**.

For example, when working with millions of entities, EF Core might use up to **2,000 MB**, while bulk insert in Entity Framework Extensions  needs only around **400 MB**. That’s a huge difference, especially in memory-constrained environments.

In this benchmark, each **Order** entity includes **5 OrderItems**. We use `IncludeGraph` to automatically handle related entities during the insert—so you don’t need to manually insert children or worry about setting their foreign keys.

| Operation                           | 1,000 Entities | 2,000 Entities | 5,000 Entities |
| :---------------------------------- | -------------: | -------------: | -------------: |
| SaveChanges                         | 1,475 ms       | 2,600 ms       | 6,500 ms       |
| BulkInsert + IncludeGraph           | 260 ms         | 450 ms         | 900 ms         |
| BulkInsertOptimized + IncludeGraph  | 200 ms         | 350 ms         | 800 ms         |

👉 [Try our Online Benchmark](https://dotnetfiddle.net/xD6Bxk)

⚠️ On .NET Fiddle, you won’t be able to run `SaveChanges` with more than around **1,800 entities** before it crashes due to memory limits. But if you **comment out** the `SaveChanges` call, you’ll see that our Entity Framework Extensions library handles **5,000 entities** just fine. This helps prove an important point: performance isn’t only about speed—**memory usage matters too**.

### EF6 vs Entity Framework Extensions

In EF6, the `SaveChanges` method makes one database round-trip for every entity it needs to insert. If you have hundreds or thousands of entities, using Entity Framework Extensions is a must-have for this version. Otherwise, you're making your users wait forever for the save to complete.

| Operation                            | 1,000 Entities | 2,000 Entities | 5,000 Entities |
| :----------------------------------- | -------------: | -------------: | -------------: |
| SaveChanges                          | 1,000 ms       | 2,000 ms       | 5,000 ms       |
| BulkInsert (Outputting values)       | 90 ms          | 115 ms         | 150 ms         |
| BulkInsert (Not Outputting values)   | 30 ms          | 35 ms          | 60 ms          |

👉 [Try our Online Benchmark](https://dotnetfiddle.net/pSpD10)

In other words, to save 5,000 entities:
- **BulkInsert (Outputting values)** is about **30x faster**, reducing insert time by **97%**.
- **BulkInsert (Not Outputting values)** is about **85x faster**, reducing insert time by **99%**.

## Bulk Insert Options

### Configuring Options with Entity Framework Extensions

We already saw in a previous article [Configuring Options](/configure-options) how to pass options to the `BulkInsert` method in Entity Framework Extensions — but here’s a quick recap:

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

// Using a lambda expression (only works with one option)
context.BulkInsert(list, options => options.InsertKeepIdentity = true);

// Using a lambda expression with a body (works with one or multiple options)
context.BulkInsert(list, options =>
{
    options.InsertKeepIdentity = true;
    options.ColumnPrimaryKeyExpression = x => new { x.ID };
});

// Using a `BulkOperationOption` instance
var options = context.CreateBulkOptions<EntitySimple>();
options.InsertKeepIdentity = true;
options.ColumnPrimaryKeyExpression = x => new { x.ID };

context.BulkInsert(list, options);
```

> 💡 Tip: Using a `BulkOperationOption` instance is useful when you want to reuse the same configuration across multiple operations or keep your setup code more organized.

### Common Options in Entity Framework Extensions

- Bulk Insert Behavior
   - **InsertIfNotExists:** Set to `true` if you only want to insert entities that don’t already exist in your database.
   - **InsertKeepIdentity:** Set to `true` if you want to insert entities with their identity value. For SQL Server, the library will automatically handle the `SET IDENTITY_INSERT [tableName] ON` and `SET IDENTITY_INSERT [tableName] OFF` commands.
   - **InsertNotMatchedAndFormula:** Specify a hardcoded SQL if you want to add custom logic to filter which rows should be inserted.
   - **InsertPrimaryKeyAndFormula:** Specify a hardcoded SQL to include additional logic—along with the primary key—to check if the entity matches an existing row in the database. Only rows that also match the formula will be inserted. This option usually only makes sense when `InsertIfNotExists = true`.
   - **InsertStagingTableFilterFormula:** Specify a hardcoded SQL if you want to filter which rows should be inserted using a staging table.
- Behavior
   - **AutoTruncate:** Set to `true` if you want string values to be automatically truncated to match the maximum database length before being inserted. This option is especially useful because `SqlCommand` and `SqlBulkCopy` can behave differently when a string is too long. (See [Issue #333](https://github.com/zzzprojects/EntityFramework-Extensions/issues/333#issuecomment-1041494634))
   - **ExplicitValueResolutionMode:** Specify how explicit values for columns (that aren’t usually expected to be set) should be handled. In EF Core, these values are always inserted. In EF Extensions, you need to tell how you want to handle them. [Learn more here](https://entityframework-extensions.net/explicit-value-resolution-mode)
   - **IncludeGraph:** Set to `true` if you want to insert both the main entities and their related entities. For example, if you pass a list of `Order` that includes `OrderItem`, both will be inserted. Be careful: if you want to apply specific options to a related entity type, you’ll need to configure them using `IncludeGraphBuilder`.
   - **IncludeGraphBuilder:** Required only if `IncludeGraph = true` **and** you need to customize how a related entity type is inserted. Use a lambda expression to control how each entity in the graph should be inserted — for example, to define how child entities are linked to their parent or how IDs should be propagated.
- Properties & Columns
   - **ColumnInputExpression:** Choose which properties should be inserted by using a lambda expression to select them. All other properties will be ignored.
   - **ColumnInputNames:** Choose which properties should be inserted by using a list of strings to select them. All other properties will be ignored.
   - **ColumnPrimaryKeyExpression:** Choose which properties should be part of the key by using a lambda expression. Only rows that match the key will be inserted. This option only works when `InsertIfNotExists = true`.
   - **ColumnPrimaryKeyNames:** Choose which properties should be part of the key by using a list of strings. Only rows that match the key will be inserted. This option only works when `InsertIfNotExists = true`.
   - **IgnoreOnInsertExpression:** Choose which properties should be ignored by using a lambda expression to select them. All other properties will be inserted.
   - **IgnoreOnInsertNames:** Choose which properties should be ignored by using a list of strings to select them. All other properties will be inserted.
- Optimization
   - **AutoMapOutputDirection:** Set to `false` to disable the default output mapping. This can dramatically improve performance when you don’t need to retrieve values normally returned by the database (like identity, computed, or default values). Alternatively, you can use the [BulkInsertOptimized](/bulk-insert-optimized) method for even faster inserts.
   - **Batch:** Customize the `BatchSize`, `BatchTimeout`, and `BatchDelayInterval` to improve performance and control how inserts are grouped and executed.
   - **Hint:** Use `QueryHint` or `TableHintSql` to apply SQL hints for additional performance tuning.
   - **UseTableLock:** Set to `true` to lock the destination table during the insert operation, which can improve performance by reducing row-level locks and avoiding lock escalation. This is especially useful when inserting a large number of rows.
- Providers Specific
   - **OracleInsertTableHint:** Gets or sets a "INSERT" hint (for BulkInsert) for ORACLE only.
   - **OracleSelectInsertIfNotExistsTableHint:** Gets or sets a "SELECT" hint (for BulkInsert with the option 'InsertIfNotExists') for ORACLE only.
   - **UsePostgreSqlInsertOnConflictDoNothing:** Set to `true` if you want to silently ignore any conflict that would otherwise trigger a constraint violation error, such as a duplicate key.
   - **UsePostgreSqlInsertOverridingSystemValue:** Set to `true` if you want the values provided in the `INSERT` statement to take precedence over any default values defined at the database level for system columns. This is useful when you need to insert explicit values for columns like timestamps that are normally managed by the database.
   - **UsePostgreSqlInsertOverridingUserValue:** Set to `true` if you want the values in the `INSERT` statement to override any user-defined default values set at the database level. This is helpful when you want the application's data to take priority — especially during automated or bulk inserts.
   - **SqlBulkCopyOptions:** Specify the default options to use when `SqlBulkCopy` is used to insert directly into the destination table (SQL Server only).  
  Default value: `(int)(DynamicSqlBulkCopyOptions.FireTriggers | DynamicSqlBulkCopyOptions.CheckConstraints)`
- General
   - **Audit:** Track inserted entities by using the `UseAudit` and `AuditEntries` options. [Learn more here](/audit)
   - **FutureAction:** Batch multiple insert operations and execute them later using the `ExecuteFuture` or `ExecuteFutureAsync` methods.
   - **Log:** Log all executed SQL statements using the `Log`, `UseLogDump`, and `LogDump` options. [Learn more here](/logging)
   - **RowsAffected:** Use `UseRowsAffected = true`, then access `ResultInfo.RowsAffected` or `ResultInfo.RowsAffectedInserted` to get the number of entities inserted. [Learn more here](/rows-affected)

## Troubleshooting

### Explicit Value Not Inserted

A major difference between `BulkInsert` and `SaveChanges` is how explicit values are handled.

In EF6, explicit values were always ignored—even if you specified a value for a column with a default value. However, EF Core changed this behavior and now automatically inserts the value you specify.

By default, our library still follows the same logic we used for EF6. But don’t worry—you can get the same behavior as EF Core by using the [ExplicitValueResolutionMode](/explicit-value-resolution-mode#explicitvalueresolutionmode) option.

Here’s how you can do it:

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

context.BulkInsert(customers, options => { 
		options.ExplicitValueResolutionMode = Z.EntityFramework.Extensions.ExplicitValueResolutionMode.SmartDefaultValueOnBulkInsert;
	});
```

### String Too Long Error on SQL Server (`AutoTruncate`)

When working with **SQL Server**, you might encounter an error if a string value is longer than the column size. This happens because `SqlCommand` and `SqlBulkCopy` behave differently:

* With **no option set** (default = `null`):
  * `SqlCommand` will work.
  * `SqlBulkCopy` will throw an error if the string is too long.
* When `AutoTruncate = true`:
  * If the string length is too long, **no error** will be thrown. Both `SqlCommand` and `SqlBulkCopy` automatically truncate the value to fit the column length.
* When `AutoTruncate = false`:
  * If the string length is too long, an **error will always be thrown**. Both `SqlCommand` and `SqlBulkCopy` enforce the column limit strictly.

👉 This is a **SQL Server–specific issue**. For backward compatibility, we cannot change the default behavior. But you are free to choose the behavior you prefer.

You can even set a global default for all operations in your project:

```csharp
EntityFrameworkManager.BulkOperationBuilder = builder =>
{
    builder.AutoTruncate = false; // or true
};
```

This way, you avoid repeating the setting for every call to `BulkInsert`.

## Limitations

### Hidden Navigation (EF6 only)

The `BulkInsert` method doesn’t rely on the `ChangeTracker` by default to maximize performance — unless there’s no other option.

For example, let’s say you want to insert a list of `InvoiceItem`, but there’s no direct navigation property or relation set toward the parent `Invoice`. In this case, you’ll need to add the parent entities to the `ChangeTracker`. This helps EF find and link the related `Invoice` for each `InvoiceItem`.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

try
{
    context.BulkInsert(items);
}
catch
{
    Console.WriteLine("An error is thrown because the Invoice relation cannot be found.");
}

context.Invoices.AddRange(invoices);

// The ChangeTracker is used in this case
context.BulkInsert(items);
```

[Online Example](https://dotnetfiddle.net/vKlII0)

## ✅ Conclusion

The EF6 / EF Core BulkInsert method is one of the most powerful and popular features in **Entity Framework Extensions**.

As you've seen in this article, it goes far beyond just improving performance compared to `SaveChanges`. It also gives you **full control** over how your data gets inserted — with dozens of options to handle identity values, relationships, and performance tuning.

Whether you're working with **EF Core or EF6**, need to insert **simple rows or complex graphs**, or want to **optimize for speed or memory**, `BulkInsert` has you covered.

So why settle for slower inserts and more code?

👉 [Try it now](https://entityframework-extensions.net/download), and see for yourself why thousands of developers — and over **5,000 companies** — rely on Entity Framework Extensions to handle their bulk operations.
