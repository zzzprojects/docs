---
Name: AllowConcurrency
LastMod: 2023-03-09
---

# AllowConcurrency

The AllowConcurrency option is used in Entity Framework extensions to enable or disable concurrent updates to the database.

When using Entity Framework to update data in a database, multiple users may try to update the same record at the same time, leading to conflicts and errors. The AllowConcurrency option can be used to handle these conflicts by either allowing or disallowing concurrent updates.

If AllowConcurrency is set to false, EF Extensions will allow multiple users to update the same record at the same time. If a conflict occurs, Entity Framework will overwrite the existing data with the updated data from the most recent update.

If AllowConcurrency is set to true, EF Extensions will not allow concurrent updates to the same record. If a conflict occurs, Entity Framework will throw an exception, indicating that the record has been updated by another user and the current update cannot be applied.