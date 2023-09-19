---
title: The Model Snapshot In Entity Framework Core
description: The role of the ModelSnapshot file in Entity Framework Core Migrations is to store a snapshot of the current state of your model
canonical: /migrations/model-snapshot
status: Published
lastmod: 2023-09-15
---

# EF Core Model Snapshot

In EF Core, a model snapshot is a representation of the current state of the model that is used to detect changes made to the model during development and update the database schema accordingly.

 - A model snapshot is generated automatically by EF Core when you build your project, and is stored in a file named `<YourContext>ModelSnapshot.cs`.
 - The model snapshot is one of the [3 migration files](/migrations/migration-files).
 - The file is added to the Migrations folder when the first migration is created, and updated with each subsequent migration. 
 - It enables the migrations framework to calculate the changes required to bring the database up to date with the model.
 
The model snapshot is created whenever you use the [Add Migration](/migrations/add-migration) or [Remove Migration](/migrations/remove-migration) command.

EF Core uses the model snapshot to compare the current state of the model to the state it was in when the snapshot was created and determines what changes have been made. 

 - If any changes are detected, EF Core can generate a migration that updates the database schema to match the new model.
 - You can manually generate a new model snapshot at any time by running the `Add-Migration` command in the Package Manager Console, or by using the equivalent command in the .NET Core CLI. 
 - This is often useful when you want to capture changes to the model that were made outside of the code, such as changes made directly to the database.

![Model Snapshot](/images/model-snapshot.jpg)

In previous versions, this information was stored in the database, which resulted in the need to query the database before scaffolding the code for a new migration. It was also stored as part of each migration as a resource file (.resx) per migration.  

 - [This caused problems in team environments](https://msdn.microsoft.com/en-us/data/dn481501) where each developer had their development database, often at different stages of development or was trying to apply different migrations to a shared development database at the same time.
 - When the snapshot was saved as part of each migration, it was possible to delete a migration, and in doing so, the snapshot was deleted, still leaving migrations and snapshots in sync with each other.  
 - If you delete a migration file in EF Core, the snapshot and migrations will be left out of kilter. For this reason, you are advised to get yourself into the practice of only ever using the `remove-migration` command to revert a migration. 
 - Having said that, if you do inadvertently delete a migration file from the migrations folder, EF Core will recognize this and revert the snapshot for you.

## Related Articles

- [Add Migration](/migrations/add-migration)
- [Remove Migration](/migrations/remove-migration)
- [Migration Files](/migrations/migration-files)