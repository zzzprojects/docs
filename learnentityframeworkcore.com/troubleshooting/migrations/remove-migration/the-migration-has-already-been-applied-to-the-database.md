---
id: cede3979-5693-49ad-9cea-bf9a2f966a20
position: 2
title: How to fix in EF Core: The migration '[MigrationName]' has already been applied to the database. Revert it and try again.
---

<div class="h9">Exception Message:</div>

```
PM> Remove-Migration
The migration 'AddingEFExtensions' has already been applied to the database. Revert it and try again.
```

<div class="image-outer"><img src="/images/efcore/migrations/remove-migration/troubleshooting-the-migration-has-already-been-applied-to-the-database.png" loading="lazy" alt="Exception - The migration has already been applied to the database"></div>

<div class="h9">Cause:</div>

This error arises when attempting to remove a migration that has already been committed to the database.

<div class="h9">Solution:</div>

Before using `Remove Migration`, make sure to revert the database to the state prior to the migration you intend to remove. This can typically be achieved using the [Update-Database](/migrations/update-database) command followed by the name of the migration preceding the one you wish to remove.

