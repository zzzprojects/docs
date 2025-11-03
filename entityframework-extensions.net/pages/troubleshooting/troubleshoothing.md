---
Title: Troubleshoothing Entity Framework Extensions
MetaDescription: Troubleshoothing Entity Framework Extensions
LastMod: 2025-11-03
---

# Troubleshooting Entity Framework Extensions

This page hosts various troubleshooting issues related to Entity Framework Extensions. Each issue is either explained directly here or includes a link to a dedicated page with a more detailed explanation.

## SQL Server

### Issue: 'Microsoft.Data.SqlClient.SqlException (0x80131904): Creating a savepoint is not supported with memory-optimized tables'

This issue happens when you use `SaveChanges` or `BulkSaveChanges` in EF Core inside a transaction and your table is **memory-optimized**.

Both methods create a **savepoint** before saving, which is not supported with memory-optimized tables.

You can disable savepoints with the following code:

```csharp
context.Database.AutoSavepointsEnabled = false;
```

Be careful: Entity Framework Extensions is not fully compatible with memory-optimized tables because `MERGE` statements are not supported.

## PostgreSQL

### Issue: 'Npgsql.PostgresException (0x80004005): 42P01: relation "[temporary table name]" does not exist'

This issue can happen when the `pooling mode=transaction` option is used in your connection settings.

In this mode, if a transaction is not used, **a different connection can be reused** during the same session (opened connection). Since a temporary table only exists within the same connection, this can cause the error above.

To fix this issue, change the pooling mode to `pooling mode=session`.
With this setting, the same connection will be used throughout the entire session, ensuring your temporary tables remain accessible.