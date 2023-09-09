---
title: ???
description: ???
canonical: /migrations/add-migration
status: Unpublished
lastmod: 2023-09-01
thumbnai: /images/efcore/migrations/add-migration/thumbnail-ef-core-add-migration.png
localhost: https://localhost:5005/migrations/add-migration
---

# EF Core Add Migration

The `Add Migration` command in EF Core generate a [migration file](/migrations/migration-file) by capturing any model changes since the last migration. To create this migration file, the `Add Migration` command compares the current [context](/dbcontext) with the last migration (if there is one) to detect what has changed in the [model](/model).

<img src="/images/efcore/migrations/add-migration/thumbnail-ef-core-add-migration.png" alt="EF Core Add Migration">

## How to use the Add Migration command with PMC in EF Core?

To use the `Add Migration` command with the Package Manager Console (PMC) in EF Core:

1. First, open the [Package Manager Console](/migrations/commands/pmc-commands) in Visual Studio.
2. Ensure your default project in the console is the one containing your EF Core DbContext.
3. Enter the following command:

```
Add-Migration [MigrationName]
```

See [Package Manager Console Parameters](/migrations/add-migration#what-are-the-parameters-for-the-add-migration-command-in-ef-core) for additional parameters options.

:::{.alert .alert-info}
**NOTE**: Replace `[MigrationName]` by the name you want to give to your migration.
:::

<img src="/images/efcore/migrations/add-migration/how-to-use-add-migration-command-with-pmc-in-ef-core.png" loading="lazy" alt="Add Migration - PMC Example">

## How to use the Add Migration command with .NET CLI in EF Core?

To use the `Add Migration` command with the .NET Command Line Interface (.NET CLI) in EF Core:

1. Open a [command prompt or terminal](/migrations/commands/cli-commands).
2. Navigate to the directory containing project.
3. Execute the following command:
```
dotnet ef migrations add [MigrationName]
```

See [Command Line Parameters](/migrations/add-migration#net-command-line-interface.net-cli) for additional parameters options.

:::{.alert .alert-info}
**NOTE**:
 - Replace `[MigrationName]` by the name you want to give to your migration.
:::

<img src="/images/efcore/migrations/add-migration/how-to-use-migration-add-command-with-net-cli-in-ef-core.png" loading="lazy" alt="Add Migration - CLI Example">

## What does the Add Migration command do in EF Core?

When you use the `Add Migration` command, either through the package manager console or command line, it will use EF Core Tools to execute several tasks:

1. First, EF Core Tools examine your [DbContext](/dbcontext) and [Model](/model).
2. Then EF Core Tools compare the current context with the most recent migration (if it exists) to detect any changes.
3. Based on this comparison, a new [migration file](/migrations/migration-file) is generated. This file contains the necessary code to implement those changes in the database.
4. After generating the migration, you can apply it to your database using the [`Update-Database`](/migrations/update-database) command, ensuring your database schema aligns with your models.

<img src="/images/efcore/migrations/add-migration/what-does-the-add-migration-command-do-in-ef-core.png" loading="lazy">

## What are the parameters for the Add Migration command in EF Core?

#### Package Manager Console (PMC)

The `Add Migration` command offers several options but only specifying the name is mandatory, all other options are optional: 

| Option | Description |
| ------ | ----------- |
| **-Name** | The name of your migration. While the `-Name` parameter is available, it's often more straightforward to directly specify the migration name as the first parameter. For example, `Add-Migration AddingEFExtensions` will create a migration named **AddingEFExtensions**. |
| **-OutputDir** | The output directory relative to the project in which the the [migration file](/migrations/migration-file) will be created. For example, `Add-Migration AddingEFExtensions -OutputDir MyMigrationDirectoryName` will add the migration file in the **MyMigrationDirectoryName** directory. |
| **-Namespace** | The namespace to use for the generated classes in the migration file. If omitted, the following namespace `[ProjectNamespace].[OutputDirectoryName]` will be used. For example, `Add-Migration AddingEFExtensions -Namespace Z.MyMigrationNamespace` will add generated classes in the **Z.MyMigrationNamespace** namespace. |
| **-Context** | The context class to use to generate the migration file, it can be either only the `name` or the `fullname` including namespaces. For example, `Add-Migration AddingEFExtensions -Context Z.MyContextName` will generate a migration for the **Z.MyContextName** context. |
| **-Project** | The project that contains the context to use to generate the migration file. If omitted, the default project in the Package Manager Console is used. For example, `Add-Migration AddingEFExtensions -Project Z.MyProjectName` will generate a migration for the context in the **Z.MyProjectName** project. |
| **-StartupProject** | The project that contains the configurations and settings. If omitted, the startup project of your solution is used.  For example, `Add-Migration AddingEFExtensions -StartupProject Z.MyStartupProjectName` will generate a migration by using configuration and settings of the **Z.MyStartupProjectName** project. |

**Tips**:

- **DO NOT**: Use `-Name`, just directly specify the name as the first parameter to the `Add-Migration` command.
- **DO NOT**: Use `-OutputDir` after the first migration, EF Core Tools is smart enough to find the output directory.
- **DO NOT**: Use `-Project`, just select the default project in the console _(personal preference)_.

:::{.alert .alert-info}
**NOTE**: While `-Project` and `-StartupProject` might appear similar, they serve different purposes. The `-Project` specifies which project contains the context file, and the `-StartupProject` indicates which project contains the application's configurations and settings.
:::

#### .NET Command Line Interface (.NET CLI)

For those working outside of Visual Studio or preferring the command line, EF Core offers similar functionality with a slightly different syntax:

| Option | Short |  Description | 
| ------ | ----- |  ----------- | 
| **[MigrationName]** | | The name of your migration (specified directly after the `add` command in `dotnet ef migrations add [MigrationName]`. |  
| **--output-dir** | **-o** | The output directory relative to the project. |  
| **--namespace** | **-n** | The namespace to use for the generated classes in the migration file. |  
| **--context** | **-c** | The context class to use to generate the migration file. |  
| **--project** | **-p** | The project that contains the context to use to generate the migration file. |  
| **--startup-project** | **-s** | The project that contains the configurations and settings. |

```csharp
dotnet ef migrations add AddingEFExtensions -o MyMigrationDirectoryName -n Z.MyMigrationNamespace -c Z.MyContextName -p Z.MyProjectName -s Z.MyStartupProjectName
```

:::{.alert .alert-info}
**NOTE**: Refer to the documentation above about [Package Manager Console Parameters](/migrations/add-migration#what-are-the-parameters-for-the-add-migration-command-in-ef-core) for examples and tips.
:::

## Which files are generated by the Add Migration command in EF Core?

When utilizing the `Add Migration` command in EF Core, it generates three essential files:

1. **[Timestamp]_[MigrationName].cs**: The main migration file with the `Up()` and `Down()` methods. The `Up()` method contains the code when a migration is applied, while the `Down()` method contains the code to apply when a migration is reverted.
2. **[Timestamp]_[MigrationName].Designer.cs**: A snapshot of your model created with a [fluent-api](/configuration/fluent-api).
3. **[ContextClassName]ModelSnapshot.cs**: The latest snapshot of your current model. The code is identical to the latest `[Timestamp]_[MigrationName].Designer.cs` generated.  This file is created only during the initial migration and updated for each subsequent migration.

The `[Timestamp]` element is created from the date and hour at which the migration was created, thereby providing a unique identifier for the migration instance.

<img src="/images/efcore/migrations/add-migration/which-file-are-generated-by-add-migration-command-in-ef-core.png" loading="lazy" alt="Add Migration - Files Generated">

Learn more about [migration files generated](/migrations/migration-file) and how to customize them with custom SQL.

## What is inside the main migration file generated?

In EF Core, the migration file serves to know how the database should evolve. This file typically contains two primary methods:

- **Up**: This method contains the changes to apply to the database when applying a migration.
- **Down**: This method contains the changes to apply to the database when reverting a migration.

Beyond the auto-generated code, EF Core allows the execution of custom SQL commands within the migrations. This capability can be especially useful for seeding data, modifying or deleting existing records:

```csharp
using Microsoft.EntityFrameworkCore.Migrations;

namespace Z.MyMigrationNamespace
{
    public partial class AddingEFE : Migration
    {
        protected override void Up(MigrationBuilder migrationBuilder)
        {
            migrationBuilder.CreateTable(
                name: "Customers",
                columns: table => new
                {
                    CustomerID = table.Column<int>(nullable: false)
                        .Annotation("SqlServer:ValueGenerationStrategy", SqlServerValueGenerationStrategy.IdentityColumn),
                    Name = table.Column<string>(maxLength: 255, nullable: false)
                },
                constraints: table =>
                {
                    table.PrimaryKey("PK_Customers", x => x.CustomerID);
                });
				
            // CUSTOM SQL commands
            migrationBuilder.Sql(@"
                INSERT INTO Customers(Name) VALUES ('ZZZ Projects');
            ");
        }

        protected override void Down(MigrationBuilder migrationBuilder)
        {
            migrationBuilder.DropTable(
                name: "Customers");
			
            // CUSTOM SQL commands
            migrationBuilder.Sql(@"
                DELETE FROM Customers WHERE Name = 'ZZZ Projects';
            ");
        }
    }
}
```

:::{.alert .alert-warning}
**NOTE**: It's crucial to ensure that if you seed or modify any data when you apply the migration (`Up` method) that you appropriately revert this change when rolling back a migration (using the `Down` method).
:::

## Troubleshoothing

#### How to fix in EF Core: The term 'Add-Migration' is not recognized as the name of a cmdlet, function, script file, or operable program.

<img src="/images/efcore/migrations/add-migration/exception-the-term-add-migration-is-not-recognized-as-the-name-of-a-cmdlet.png" loading="lazy" alt="Exception - The term Add Migration is not recognized">

**Exception Message**:

```
PM> Add-Migration AddingEFExtensions
Add-Migration : The term 'Add-Migration' is not recognized as the name of a cmdlet, 
function, script file, or operable program. Check the spelling of the name, or if a path 
was included, verify that the path is correct and try again.
At line:1 char:1
+ Add-Migration AddingEFExtensions
+ ~~~~~~~~~~~~~
    + CategoryInfo          : ObjectNotFound: (Add-Migration:String) [], CommandNotFoundExc 
   eption
    + FullyQualifiedErrorId : CommandNotFoundException
 
```

**Cause**: This error arises when the [Microsoft.EntityFrameworkCore.Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools) package is not referenced in your `Migrations Project`.

**Solution**: Make sure you have installed or referenced the [Microsoft.EntityFrameworkCore.Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools) package in your `Migrations Project`. 

You can add this package via the NuGet Package Manager with:

```
Install-Package Microsoft.EntityFrameworkCore.Tools
```

Or through the .NET CLI with:

```
dotnet add package Microsoft.EntityFrameworkCore.Tools
```

---

#### How to fix in EF Core: Your startup project '[StartupProjectName]' doesn't reference Microsoft.EntityFrameworkCore.Design. This package is required for the Entity Framework Core Tools to work. Ensure your startup project is correct, install the package, and try again.

<img src="/images/efcore/migrations/add-migration/exception-your-startup-project-doesn-t-reference-microsoft-entitiyframeworkcore-design.png" loading="lazy" alt="Exception - Startup project not reference Microsoft.EntityFrameworkCore.Design">

**Exception Message**:

```
PM> Add-Migration AddingEFExtensions
Build started...
Build succeeded.
Your startup project 'Z.MyStartupProjectName' doesn't reference Microsoft.EntityFrameworkCore.Design. This package is required for the Entity Framework Core Tools to work. Ensure your startup project is correct, install the package, and try again.
```
 
**Cause**: This error arises when the [Microsoft.EntityFrameworkCore.Design](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Design) package is not referenced in your `Startup Project`.

**Solution**: Make sure you have installed or referenced the [Microsoft.EntityFrameworkCore.Design](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Design) package in your `Startup Project`. 

You can add this package via the NuGet Package Manager with:

```
Install-Package Microsoft.EntityFrameworkCore.Design
```

Or through the .NET CLI with:

```
dotnet add package Microsoft.EntityFrameworkCore.Design
```

---

#### How to fix in EF Core: dotnet command not found - Could not execute because the specified command or file was not found.

<img src="/images/efcore/migrations/add-migration/exception-dotnet-command-not-found.png" loading="lazy" alt="Exception - dotnet command not found">

**Exception Message**:

```
dotnet ef migrations add [MigrationName]
Could not execute because the specified command or file was not found.
Possible reasons for this include:
  * You misspelled a built-in dotnet command.
  * You intended to execute a .NET program, but dotnet-ef does not exist.
  * You intended to run a global tool, but a dotnet-prefixed executable with this name could not be found on the PATH.
```

**Cause**: This error arises when the `dotnet-ef` tool is not installed globally or locally.

**Solution**: Install the `dotnet-ef` tool:

```cmd
// for the latest version:
dotnet tool install --global dotnet-ef

// for a specific version:
dotnet tool install --global dotnet-ef --version 8.*
dotnet tool install --global dotnet-ef --version 7.*
dotnet tool install --global dotnet-ef --version 6.*
dotnet tool install --global dotnet-ef --version 5.*
dotnet tool install --global dotnet-ef --version 3.*
```

---