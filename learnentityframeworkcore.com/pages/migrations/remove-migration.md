---
title: Remove Migration Command in Entity Framework Core
description: Discover how to effectively remove migrations in EF Core. This guide provides step-by-step instructions for using the Remove Migration command via both the Package Manager Console (PMC) and the .NET Command Line Interface (CLI). Learn to safely delete the latest unapplied migration files and understand the available parameters for seamless migration management.
canonical: /migrations/remove-migration
status: Published
lastmod: 2025-07-13
thumbnail: /images/efcore/migrations/remove-migration/thumbnail-ef-core-remove-migration.png
localhost: https://localhost:5005/migrations/remove-migration
---

# EF Core Remove Migration

Removing a migration in EF Core is the process of deleting [files](/migrations/migration-files) of the most recent unapplied migration created. To remove those migration files, a `Remove Migration` command must be run. This command checks the database for all applied migrations and deletes the most recent migration files only if they haven't been applied to the database yet.

There are 2 ways to run the `Remove Migration` command and both will be covered in this article:

- `Remove-Migration`: When using the Package Manager Console (PMC)
- `dotnet ef migrations remove`: When using the .NET Command Line Interface (.NET CLI)

<div class="image-outer"><img src="/images/efcore/migrations/remove-migration/thumbnail-ef-core-remove-migration.png" loading="lazy" alt="EF Core Remove Migration"></div>

The `Remove Migration` command is one of the 3 main migration commands:

- [Add Migration](/migrations/add-migration)
- [Update Database](/migrations/update-database)
- Remove Migration

## How to use the Remove-Migration command with PMC in EF Core?

To use the `Remove-Migration` command with the Package Manager Console (PMC) in EF Core:

1. Launch the [Package Manager Console](/migrations/commands/pmc-commands) within Visual Studio.
2. Ensure the default project in the console is the one containing your EF Core DbContext.
3. Enter the following command:

```
Remove-Migration
```

:::{.alert .alert-info}
**NOTE**: If you want to remove multiple migrations, you will need to run the command multiple times.
:::

See [Package Manager Console Parameters](#what-are-the-parameters-for-the-remove-migration-command-in-ef-core) for additional parameters options.

<div class="image-outer"><img src="/images/efcore/migrations/remove-migration/how-to-use-remove-migration-command-with-pmc-in-ef-core.png" loading="lazy" alt="Remove Migration - PMC Example"></div>

## How to use the Remove Migration command with .NET CLI in EF Core?

To use the `dotnet ef migrations remove` command with the .NET Command Line Interface (.NET CLI) in EF Core:

1. Open a [command prompt or terminal](/migrations/commands/cli-commands).
2. Navigate to the directory containing your context.
3. Run the following command:

```
dotnet ef migrations remove
```

:::{.alert .alert-info}
**NOTE**: If you want to remove multiple migrations, you will need to run the command multiple times.
:::

See [Command Line Parameters](#net-command-line-interface.net-cli) for additional parameters options.

<div class="image-outer"><img src="/images/efcore/migrations/remove-migration/how-to-use-remove-migration-command-with-net-cli-in-ef-core.png" loading="lazy" alt="Remove Migration - CLI Example"></div>

## What does the Remove-Migration command accomplish in EF Core?

When you use the `Remove-Migration` command with PMC or `dotnet ef migrations remove` with .NET CLI, both utilize EF Core Tools to perform several tasks:

1. First, EF Core Tools fetch all migrations that have been applied to your database.
2. Then, EF Core Tools check if the latest migration has been applied.
   a. An error will be thrown if that's the case.
3. The EF Core Tools then remove the latest unapplied migration by:
   a. Removing the corresponding [migration files](/migrations/migration-files) from your project.
   b. Updating the [Model Snapshot](/migrations/model-snapshot) to reflect the state before the latest migration.

## What are the parameters for the Remove Migration command in EF Core?

#### Package Manager Console (PMC)

The `Remove-Migration` command with PMC offers several optional options:

| Option | Description |
| ------ | ----------- |
| **-Force** | When used, the `Remove-Migration -Force` command will remove the migration regardless of whether it has already been applied to the database. |
| **-Context** | The context class to use that contains the migration you want to remove, it can be either the `name` or the `fullname` including namespaces. For example, `Remove-Migration -Context Z.MyContextName` will remove the migration for the **Z.MyContextName** context. |
| **-Project** | The project containing the context used to remove the migration file. If omitted, the default project in the Package Manager Console is used. For example, `Remove-Migration -Project Z.MyProjectName` will remove the migration for the context in the **Z.MyProjectName** project. |
| **-StartupProject** | The project with the configurations and settings. If omitted, the startup project of your solution is used. For example, `Remove-Migration -StartupProject Z.MyStartupProjectName` will remove the migration using the configurations and settings of the **Z.MyStartupProjectName** project. |

```csharp
Remove-Migration -Force -Context Z.MyContextName -Project Z.MyProjectName -StartupProject Z.MyStartupProjectName
```

:::{.alert .alert-info}
**NOTE**: The `-Project` option specifies which project contains the [context](/dbcontext), while the `-StartupProject` option indicates the project that has the application's configurations and settings.
:::

#### .NET Command Line Interface (.NET CLI)

For those working outside of Visual Studio or preferring the command line, EF Core offers similar functionality but with a slightly different syntax:

| Option | Short |  Description |
| ------ | ----- |  ----------- |
| **--force** | **-f** | Forces the removal of the migration even if it has already been applied to the database. |  
| **--context** | **-c** | Specifies which DbContext to use. |  
| **--project** | **-p** | The project containing the migrations to be removed. |  
| **--startup-project** | **-s** | The project with the required configurations and settings. |

```csharp
dotnet ef database update [TargetMigration] -c MyContext -p MigrationsProject -s StartupProject
```

:::{.alert .alert-info}
**NOTE**: Refer to the documentation above about [Package Manager Console Parameters](/migrations/update-database#what-are-the-parameters-for-the-remove-migration-command-in-ef-core) for more examples.
:::

## Related Articles

- [Add Migration](/migrations/add-migration)
- [Update Database](/migrations/update-database)
- [Migration Files](/migrations/migration-files)
- [Model Snapshot](/migrations/model-snapshot)