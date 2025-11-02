---
Title: Limitations Entity Framework Extensions
MetaDescription: Limitations Entity Framework Extensions
LastMod: 2025-10-04
---

# Limitations in Entity Framework Extensions

This page lists various **limitations** related to Entity Framework Extensions.
Each limitation is either explained directly here or includes a link to another page with a more detailed explanation.

## Bulk Merge

### Insert is ignored if the insert column list is empty

For all providers **except `SQL Server`**, if there are no columns to insert into, the insert part will be ignored instead of inserting default values.