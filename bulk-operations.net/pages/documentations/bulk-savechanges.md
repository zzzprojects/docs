# Bulk SaveChanges

## Description

Execute an INSERT/UPDATE/DELETE operation using the DataRowState of the DataTable.

### Example - Bulk SaveChanges
```csharp
var dt = new DataTable();
// ...seed...

var bulk = new BulkOperation(connection);

// Easy to customize
bulk.BatchSize = 1000;

// Easy to use
bulk.BulkSaveChanges(dt);
```

Try it: [.NET Core](https://dotnetfiddle.net/fK4QYL) | [.NET Framework](https://dotnetfiddle.net/sm4a1B)
