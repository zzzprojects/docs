---
Title: Troubleshoothing Entity Framework Extensions
MetaDescription: Troubleshoothing Entity Framework Extensions
LastMod: 2025-10-04
---

# Troubleshooting Entity Framework Extensions

This page hosts various troubleshooting issues related to Entity Framework Extensions. Each issue is either explained directly here or includes a link to a dedicated page with a more detailed explanation.

## PostgreSQL

### Issue: Npgsql.PostgresException (0x80004005): 42P01: relation "[temporary table name]" does not exist

This issue can happen when the `pooling mode=transaction` option is used in your connection settings.

In this mode, if a transaction is not used, **a different connection can be reused** during the same session (opened connection). Since a temporary table only exists within the same connection, this can cause the error above.

To fix this issue, change the pooling mode to `pooling mode=session`.
With this setting, the same connection will be used throughout the entire session, ensuring your temporary tables remain accessible.