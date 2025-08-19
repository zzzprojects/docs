---
Name: Logging in Entity Framework Extensions
MetaDescription: Learn how to use logging options in Entity Framework Extensions for EF Core and EF6, including Log, LogDump, and UseLogDump. Capture SQL statements, parameters, and execution details with flexible configuration methods.
LastMod: 2025-08-18
---

# 📜 Logging in Entity Framework Extensions/n Capture executed SQL, parameters, and execution time

The `Logging` feature in Entity Framework Extensions lets you **capture and analyze SQL statements, parameters, and execution details** during [bulk operations](/bulk-extensions) with your EF Core context.

You can choose between two approaches:

* Use a **delegate (`Log`)** to handle log messages immediately as they are generated.
* Use a **`StringBuilder` (`LogDump`)** to collect all log messages when `UseLogDump` is enabled.

## 🏷️ Log

The `Log` option in Entity Framework Extensions lets you **define an action** that runs whenever an "Information" log message is generated.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;
using System.Text;

// Example 1: Log into a StringBuilder
var sb = new StringBuilder();
context.BulkMerge(list, options =>
{
    options.Log = message => sb.AppendLine(message);
});
Console.WriteLine(sb.ToString());

// Example 2: Log directly to the console
context.BulkMerge(list, options =>
{
    options.Log = Console.WriteLine;
});
```

👉 Best for redirecting logs directly into custom handlers (e.g., **console output, files, or third-party logging frameworks**).

---

## 🏷️ LogDump + UseLogDump

The `LogDump` property is a `StringBuilder` that acts as a **container for log messages**.

To enable it, you must also set the `UseLogDump` property to `true`.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;
using System.Text;

var sb = new StringBuilder();

context.BulkMerge(list, options =>
{
    options.UseLogDump = true;  // Enable log dump
    options.LogDump = sb;       // Collect messages into StringBuilder
});

Console.WriteLine(sb.ToString());
```

👉 Best for scenarios where you want to **collect all log messages in one place** and review them after the operation completes.

---

## 🔎 Log vs LogDump

| Feature           | **Log** (`options.Log`)                                                     | **LogDump** (`options.LogDump + UseLogDump`)                  |
| ----------------- | --------------------------------------------------------------------------- | ------------------------------------------------------------- |
| **How it works**  | Executes an action for each log message as it happens                       | Collects all messages into a `StringBuilder`                  |
| **When executed** | Real-time, during the operation                                             | After the operation finishes (messages available all at once) |
| **Best for**      | Redirecting messages to custom handlers (console, file, third-party logger) | Reviewing or debugging all messages at once                   |
| **Flexibility**   | High — you decide what to do with each message                              | Lower — limited to storing in `StringBuilder`                 |
| **Typical usage** | `options.Log = message => sb.AppendLine(message);`                                          | `options.UseLogDump = true; options.LogDump = sb;`            |

---

## 📌 When to Use

Logging is useful whenever you need to:

- Debug bulk operations by reviewing executed SQL.
- Capture logs for auditing or monitoring.
- Redirect SQL output into custom loggers like **NLog**, **Serilog**, or **log4net**.

---

## ⭐ Why It’s Useful

With logging you can:

- See what SQL is executed behind the scenes.
- Diagnose performance or parameter issues.
- Store executed SQL for debugging, testing, or compliance.
---

## Log Message Example

## Example 1 — Insert with Identity

Here we use the option `options.InsertKeepIdentity = true;` so our insert keeps the **identity values** we provide.

The log clearly shows what happened:

* `IDENTITY_INSERT` was turned **ON**, the 3 rows were inserted, then it was turned **OFF**.
* The exact **SQL command text** is displayed.
* Each **parameter value and type** is listed.
* The **command timeout**, **execution start and end time**, and the **number of results** are all logged.

```
-- Executing Command:
SET IDENTITY_INSERT [EntitySimples] ON;
INSERT INTO [EntitySimples] 
( [ID], [ColumnString] )
SELECT [ID], [ColumnString]  
FROM (SELECT TOP 100 PERCENT * FROM (SELECT @0_0 AS [ID], @0_1 AS [ColumnString], @0_2 AS ZZZ_Index
UNION ALL SELECT @1_0 AS [ID], @1_1 AS [ColumnString], @1_2 AS ZZZ_Index
UNION ALL SELECT @2_0 AS [ID], @2_1 AS [ColumnString], @2_2 AS ZZZ_Index) AS StagingTable ORDER BY ZZZ_Index) AS StagingTable   
;
SET IDENTITY_INSERT [EntitySimples] OFF;
-- @0_0: 1 (Type = Int32, Size = 4)
-- @0_1: Entity Framework Extensions (Type = String, Size = -1)
-- @0_2: 0 (Type = Int32, Size = 0)
-- @1_0: 2 (Type = Int32, Size = 4)
-- @1_1: Dapper Plus (Type = String, Size = -1)
-- @1_2: 1 (Type = Int32, Size = 0)
-- @2_0: 3 (Type = Int32, Size = 4)
-- @2_1: C# Eval Expression (Type = String, Size = -1)
-- @2_2: 2 (Type = Int32, Size = 0)
-- CommandTimeout:0
-- Executing at 2025-08-19 10:25:21 AM
-- Completed at 2025-08-19 10:25:21 AM
-- Result: 3
```

### Example 2 — Bulk Insert with Staging Table

In this example, a **staging table** is created to perform a bulk insert. This is the default approach Entity Framework Extensions uses behind the scenes for many bulk operations.

The log shows each step of the process:

* Create a **temporary table** (`#ZZZProjects_...`) with the required columns.
* Create a **clustered index** on the staging table to improve performance.
* Use **`SqlBulkCopy`** to load data into the staging table.
* Execute a **`MERGE`** statement to insert rows into the destination table.
  * The `OUTPUT` clause is used here to return additional details.
* Log the **parameters** (`@IndexStart`, `@IndexEnd`) with their values and types.
* Log **timing information** and the **number of rows inserted**.
* Finally, **drop** the temporary table once the operation completes.

```
-- Executing Command:
CREATE TABLE #ZZZProjects_5e417609_6377_4596_a517_6cf250906727 ( [ColumnString] nvarchar (max)  COLLATE SQL_Latin1_General_CP1_CI_AI NULL, [ID] [sys].[int] NULL, ZZZ_Index [INT] NOT NULL )

CREATE CLUSTERED INDEX [INDEX_#ZZZProjects_5e417609_6377_4596_a517_6cf250906727] ON #ZZZProjects_5e417609_6377_4596_a517_6cf250906727 (ZZZ_Index ASC);
-- CommandTimeout:0
-- Executing at 2025-08-19 10:27:44 AM
-- Completed at 2025-08-19 10:27:44 AM
-- Result: -1


SqlBulkCopy: #ZZZProjects_5e417609_6377_4596_a517_6cf250906727
-- BulkCopyTimeout:0
Executing at 2025-08-19 10:27:44 AM
Completed at 2025-08-19 10:27:44 AM


-- Executing Command:
MERGE INTO [EntitySimples]  AS DestinationTable
USING
(
SELECT TOP 100 PERCENT * FROM #ZZZProjects_5e417609_6377_4596_a517_6cf250906727
WHERE ZZZ_Index >= @IndexStart AND ZZZ_Index <= @IndexEnd ORDER BY ZZZ_Index
) AS StagingTable
ON 1 = 2
WHEN NOT MATCHED THEN
    INSERT ( [ColumnString] )
    VALUES ( [ColumnString] )
OUTPUT
    $action,
    StagingTable.ZZZ_Index,
    INSERTED.[ID] AS [ID_zzzinserted]


;
-- @IndexStart: 0 (Type = Int32, Size = 0)
-- @IndexEnd: 2 (Type = Int32, Size = 0)
-- CommandTimeout:0
-- Executing at 2025-08-19 10:27:44 AM
-- Completed at 2025-08-19 10:27:44 AM
-- Result: 3 rows


-- Executing Command:
DROP TABLE #ZZZProjects_5e417609_6377_4596_a517_6cf250906727;
-- CommandTimeout:0
-- Executing at 2025-08-19 10:27:44 AM
-- Completed at 2025-08-19 10:27:44 AM
-- Result: -1
```

---

## 📚 Related Articles

* [Bulk Operations](/bulk-extensions)
* [Configure Options](/configure-options)

---

## 🏁 Conclusion

Logging in Entity Framework Extensions is a simple but powerful feature that helps you **see and store SQL statements executed during bulk operations**.

You can choose:

* **Custom action logging** → `Log`
* **Buffered logging** → `UseLogDump + LogDump`

With these options, you can easily integrate logging into your workflow — whether you want quick debugging, compliance tracking, or advanced integration with logging frameworks.