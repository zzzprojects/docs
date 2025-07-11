---
id: 006a3bb8-99c7-480f-957c-26dc1e635b5c
position: 5
title: What is the "__MigrationHistory" table in EF Core?
---

The `__MigrationHistory` is a table used internally by EF Core to track which migration has already been applied.

- **MigrationId**: A unique identifier for each migration. It typically combines the migration's timestamp and name.
- **ContextKey**: A unique identifier for the context to allow multiple contexts in the same database.
- **Model**: A snapshot of the model after the migration has been applied.
- **ProductVersion**: The version of EF Core used to apply the migration.