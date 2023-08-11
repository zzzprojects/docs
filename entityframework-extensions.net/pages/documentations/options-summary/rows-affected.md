---
Name: Rows Affected
LastMod: 2023-03-01
---

# Rows Affected

## RowsAffected

Getting the number of rows affected might be useful to know how many entities have been inserted, for example.

It's possible to get this information by enabling the option `UseRowsAffected = true`. Enabling this option is required since it slightly decrease the performance.

You can find the number of rows affected in the `ResultInfo` class:

- ResultInfo
   - RowsAffected
   - RowsAffectedInserted
   - RowsAffectedUpdated
   - RowsAffectedDeleted

```csharp
var resultInfo = new Z.BulkOperations.ResultInfo();

context.BulkSaveChanges(options => {
	options.UseRowsAffected = true;
	options.ResultInfo = resultInfo;
});
```

[Try it in EF Core](https://dotnetfiddle.net/PeKp75) | [Try it in EF6](https://dotnetfiddle.net/ZZdWD9)