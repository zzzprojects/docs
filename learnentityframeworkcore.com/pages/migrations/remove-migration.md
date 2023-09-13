---
title: ???
description: ???
canonical: /migrations/remove-migration
status: Unpublished
lastmod: 2023-09-01
---

```sql
SELECT [MigrationId], [ProductVersion]
FROM [__EFMigrationsHistory]
ORDER BY [MigrationId];
```

# EF Core Remove Migration

The `Remove-Migration` command in EF Core remove the last migration created. The command will remove all files created by the last [Add-Migration](/add-migration) commands.

:::{.alert .alert-info}
**NOTE**: The `Remove-Migration` command doesn't revert a database state to a previous state unlike the [Update-Migration](/update-migration#revert) command.
:::

<img src="/images/efcore/migrations/remove-migration/thumbnail-ef-core-remove-migration.png" alt="EF Core Remove Migration">

## How to use the Remove-Migration command with PMC in EF Core?

To use the `Remove-Migration` command with the Package Manager Console (PMC) in EF Core:

1. Launch the [Package Manager Console](/migrations/commands/pmc-commands) within Visual Studio.
2. Make sure the default project in the console corresponds to the one containing your EF Core DbContext.
3. Type in the console:

```
Remove-Migration
```

## How to use the Remove Migration command with .NET CLI in EF Core?

For those who prefer the .NET Command Line Interface (.NET CLI) in EF Core:

1. Open your preferred [command prompt or terminal](/migrations/commands/cli-commands).
2. Navigate to the project directory.
3. Run:

```
dotnet ef migrations remove
```

## What does the Remove Migration command accomplish in EF Core?

The `Remove Migration` command, whether executed via the package manager console or command line, performs the following:

1. Firstly, the EF Core Tools search for the latest migration created.
2. The tools will very the migration has not been applied in the database. If this is the case, an error will be thrown. You can rollback a migration with the [Update-Database](/update-database#revert) command.
3. The tools then revoke this migration, removing the corresponding [migration file](/migrations/migration-file) from your codebase. 

<img src="/images/efcore/migrations/remove-migration/what-does-the-remove-migration-command-do-in-ef-core.png" loading="lazy">

## Which files are affected by the Remove Migration command in EF Core?

Post execution of the `Remove Migration` command, 2 files will be removed and 1 modified:

1. **[Timestamp]_[MigrationName].cs**: The primary migration file containing the `Up()` and `Down()` methods is removed.
2. **[Timestamp]_[MigrationName].Designer.cs**: The model snapshot generated via [fluent-api](/configuration/fluent-api) is removed.
3. **[ContextClassName]ModelSnapshot.cs**: The latest snapshot of your current model is updated to reflect the removal of the latest migration.

## Reverting Database Changes After Removing a Migration

After executing the `Remove Migration` command, remember that your codebase is updated but the changes persist in your database. To revert those changes:

1. Use the `Update-Database` command, followed by the name of the previous migration.
2. Ensure to inspect and test your database after rollback to ensure data integrity and consistency.

:::{.alert .alert-warning}
**NOTE**: Before removing migrations, always ensure you have backups of your data. Mistakes or unexpected outcomes during the migration process might result in data loss.
:::

## Troubleshooting

#### How to address: The term 'Remove-Migration' isn't recognized as a valid command, function, script file, or operable program.

**Exception Message**:

```
PM> Remove-Migration
Remove-Migration : The term 'Remove-Migration' is not recognized as the name of a cmdlet, 
function, script file, or operable program. Verify the spelling of the name, or ensure the path 
is included and try again.
```

**Cause**: This error pops up when the [Microsoft.EntityFrameworkCore.Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools) package isn't referenced in your migrations project.

**Solution**: Ensure the [Microsoft.EntityFrameworkCore.Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools) package is installed or referenced in your migrations project.

You can install it via NuGet Package Manager:

```
Install-Package Microsoft.EntityFrameworkCore.Tools
```

Or using .NET CLI:

```
dotnet add package Microsoft.EntityFrameworkCore.Tools
```

---

#### How to address: The migration '[MigrationName]' has already been applied to the database. Revert it and try again.

**Exception Message**:

```
PM> Remove-Migration
The migration 'AddingEFExtensions' has already been applied to the database. Revert it and try again.
```

**Cause**: This error arises when attempting to remove a migration that has already been committed to the database.

**Solution**: Before using `Remove Migration`, make sure to revert the database to the state prior to the migration you intend to remove. This can typically be achieved using the `Update-Database` command followed by the name of the migration preceding the one you wish to remove.