---
title: Add Migration Command in Entity Framework Core
description: Discover how to use the 'Add Migration' command in Entity Framework Core. Learn step-by-step processes for both the Package Manager Console (PMC) and the .NET Command Line Interface (.NET CLI) to capture and implement model changes.
canonical: /migrations/add-migration
status: Published
lastmod: 2023-09-19
thumbnail: /images/efcore/migrations/add-migration/thumbnail-ef-core-add-migration.png
localhost: https://localhost:5005/migrations/add-migration
---

# EF Core Add Migration

Adding a migration in EF Core is the process of capturing model changes since the last migration and generating [migration files](/migrations/migration-files) from those changes. To create those migration files, an `Add Migration` command must be run. This command compares the current [context](/dbcontext) with the last migration created (if there was one) to detect what has changed in the [model](/model).

There are 2 ways to run the `Add Migration` command and both will be covered in this article:

- `Add-Migration`: When using the Package Manager Console (PMC)
- `dotnet ef migrations add`: When using the .NET Command Line Interface (.NET CLI)

<div class="image-outer"><img src="/images/efcore/migrations/add-migration/thumbnail-ef-core-add-migration.png" loading="lazy" alt="EF Core Add Migration"></div>

The `Add Migration` command is one of the 3 main migration commands:

- Add Migration
- [Update Database](/migrations/update-database)
- [Remove Migration](/migrations/remove-migration)

## How to use the Add Migration command with PMC in EF Core?

To use the `Add-Migration` command with the Package Manager Console (PMC) in EF Core:

1. First, open the [Package Manager Console](/migrations/commands/pmc-commands) in Visual Studio.
2. Ensure the default project in the console is the one containing your EF Core DbContext.
3. Enter the following command:

```
Add-Migration [MigrationName]
```

:::{.alert .alert-info}
**NOTE**: Replace `[MigrationName]` by the name you want to give to your migration.
:::

See [Package Manager Console Parameters](#what-are-the-parameters-for-the-add-migration-command-in-ef-core) for additional parameters options.

<div class="image-outer"><img src="/images/efcore/migrations/add-migration/how-to-use-add-migration-command-with-pmc-in-ef-core.png" loading="lazy" alt="Add Migration - PMC Example"></div>

## How to use the Add Migration command with .NET CLI in EF Core?

To use the `dotnet ef migrations add` command with the .NET Command Line Interface (.NET CLI) in EF Core:

1. Open a [command prompt or terminal](/migrations/commands/cli-commands).
2. Navigate to the directory containing your context.
3. Run the following command:
```
dotnet ef migrations add [MigrationName]
```

:::{.alert .alert-info}
**NOTE**: Replace `[MigrationName]` by the name you want to give to your migration.
:::

See [Command Line Parameters](#net-command-line-interface.net-cli) for additional parameters options.

<div class="image-outer"><img src="/images/efcore/migrations/add-migration/how-to-use-migration-add-command-with-net-cli-in-ef-core.png" loading="lazy" alt="Add Migration - CLI Example"></div>

## What does the Add-Migration command do in EF Core?

When you use the `Add-Migration` command with PMC or `dotnet ef migrations add` with .NET CLI, both utilize EF Core Tools to perform several tasks:

1. First, EF Core Tools look at your [DbContext](/dbcontext) and [Model](/model).
2. Then, EF Core Tools compare the current context against the one from the [Model Snapshot](/migrations/model-snapshot) (if there is one) to find any changes.
3. Based on this comparison, new [migration files](/migrations/migration-files) are generated. These files contain the necessary code to apply and revert those changes in the database.
4. After creating a new migration, you can apply it to your database with the [Update-Database](/migrations/update-database) command or remove it using the [Remove-Migration](/migrations/remove-migration) command.

<div class="image-outer"><img src="/images/efcore/migrations/add-migration/what-does-the-add-migration-command-do-in-ef-core.png" loading="lazy" alt="Add Migration Tasks"></div>

## What are the parameters for the Add Migration command in EF Core?

#### Package Manager Console (PMC)

The `Add-Migration` command with PMC offers several options. While specifying the migration name is mandatory (the console will ask for one if not specified), all other options are optional:

| Option | Description |
| ------ | ----------- |
| **-Name** | The name of the migration you want to create. While the `-Name` parameter is there, it's often simpler to directly specify the migration name as the first parameter. For example, `Add-Migration AddingEFExtensions` will create a migration named **AddingEFExtensions**. |
| **-OutputDir** | The output directory relative to the project where [migration files](/migrations/migration-files) will be created. For example, `Add-Migration AddingEFExtensions -OutputDir MyMigrationDirectoryName` will create the migration file in the **MyMigrationDirectoryName** directory. |
| **-Namespace** | The namespace to use for the generated classes in the migration file. If omitted, the following namespace `[ProjectNamespace].[OutputDirectoryName]` will be used. For example, `Add-Migration AddingEFExtensions -Namespace Z.MyMigrationNamespace` will add classes in the **Z.MyMigrationNamespace** namespace. |
| **-Context** | The context class to use to generate the migration file, it can be either the `name` or the `fullname` including namespaces. For example, `Add-Migration AddingEFExtensions -Context Z.MyContextName` will generate a migration for the **Z.MyContextName** context. |
| **-Project** | The project containing the context used to generate the migration file. If omitted, the default project in the Package Manager Console is used. For example, `Add-Migration AddingEFExtensions -Project Z.MyProjectName` will generate a migration for the context in the **Z.MyProjectName** project. |
| **-StartupProject** | The project with the configurations and settings. If omitted, the startup project of your solution is used. For example, `Add-Migration AddingEFExtensions -StartupProject Z.MyStartupProjectName` will make a migration using the configurations and settings of the **Z.MyStartupProjectName** project. |

```csharp
Add-Migration [MigrationName] -OutputDir MyMigrationDirectoryName -Namespace Z.MyMigrationNamespace -Context Z.MyContextName -Project Z.MyProjectName -StartupProject Z.MyStartupProjectName
```

**Tips**:
- **DO NOT** use `-Name`. Directly specify the name as the first parameter of the `Add-Migration` command.
- **DO NOT** use `-OutputDir` after the first migration. EF Core Tools can automatically locate the output directory for later migrations.
- **DO NOT** use `-Project`. Instead, select the default project in the console _(personal preference)_.

:::{.alert .alert-info}
**NOTE**: The `-Project` option specifies which project contains the [context](/dbcontext), while the `-StartupProject` option indicates the project that has the application's configurations and settings.
:::

#### .NET Command Line Interface (.NET CLI)

For those who work outside of Visual Studio or prefer the command line, EF Core Tools provide similar features for .NET CLI but with a bit different syntax:

| Option | Short |  Description | 
| ------ | ----- |  ----------- | 
| **[MigrationName]** | | The name of your migration. It's placed directly after the `add` command in `dotnet ef migrations add [MigrationName]`. |  
| **--output-dir** | **-o** | The output folder related to the project. |  
| **--namespace** | **-n** | The namespace for the created classes in the migration file. |  
| **--context** | **-c** | The context class used to create the migration file. |  
| **--project** | **-p** | The project that has the context for making the migration file. |  
| **--startup-project** | **-s** | The project with the configurations and settings. |

```csharp
dotnet ef migrations add AddingEFExtensions -o MyMigrationDirectoryName -n Z.MyMigrationNamespace -c Z.MyContextName -p Z.MyProjectName -s Z.MyStartupProjectName
```

:::{.alert .alert-info}
**NOTE**: Check the documentation above about [Package Manager Console Parameters](/migrations/add-migration#what-are-the-parameters-for-the-add-migration-command-in-ef-core) for more examples and tips.
:::

## Related Articles

- [Update Database](/migrations/update-database)
- [Remove Migration](/migrations/remove-migration)
- [Migration Files](/migrations/migration-files)
- [Model Snapshot](/migrations/model-snapshot)