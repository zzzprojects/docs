---
Name: Temporary Table
---

# Temporary Table

## TemporaryTableBatchByTable
Gets or sets the number of batches a temporary table can contain. This option may create multiple temporary tables when the number of batches to execute exceeds the specified limit.


```csharp
context.BulkMerge(options =>
{
   options.TemporaryTableBatchByTable = 0; // unlimited
});
```

---

## TemporaryTableInsertBatchSize
Gets or sets the number of records to use in a batch when inserting in a temporary table. This number is recommended to be high.


```csharp
context.BulkMerge(options =>
{
   options.TemporaryTableInsertBatchSize = 50000;
});
```

---

## TemporaryTableMinRecord
Gets or sets the minimum number of records to use a temporary table instead of using a SQL derived table.


```csharp
context.BulkMerge(options =>
{
   options.TemporaryTableMinRecord = 25;
});
```

---

## TemporaryTableSchemaName
Gets or sets the schema name to use for the temporary table.


```csharp
context.BulkMerge(options =>
{
   options.TemporaryTableSchemaName = "zzz";
});
```

---

## TemporaryTableUseTableLock
Gets or sets if the temporary table must be locked when inserting records into it.


```csharp
context.BulkMerge(options =>
{
   options.TemporaryTableUseTableLock = true;
});
```


---

## UsePermanentTable
Gets or sets if the library should `create` and `drop` a permanent table instead of using a temporary table.


```csharp
context.BulkMerge(options =>
{
   options.UsePermanentTable = true;
});
```
