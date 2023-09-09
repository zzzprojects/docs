---
title: ???
description: ???
canonical: /migrations/remove-migration
status: Unpublished
lastmod: 2023-09-01
---

## FAQ

#### What is the difference between 'Add-Migration' and 'dotnet ef migrations add' in EF Core?

Both `Add-Migration` and `dotnet ef migrations add` generate new migrations in EF Core. Their primary differences lie in their usage context:

1. **Add-Migration**:
   - Used in the Package Manager Console (PMC) within Visual Studio.
   - Requires the `Microsoft.EntityFrameworkCore.Tools` package.

2. **dotnet ef migrations add**:
   - Used in the .NET Command Line Interface (.NET CLI) outside of Visual Studio.
   - Requires both the `Microsoft.EntityFrameworkCore.Tools` and `Microsoft.EntityFrameworkCore.Design` packages.

Under the hood, they are the same, but their contexts of use differ: inside Visual Studio with PMC or outside with the .NET CLI.

#### How should I name my first migration in EF Core?

The choice of migration name is up to you, but it's commonly recommended to use `InitialCreate` for the first migration.

In fact, even EF Core recommends this naming convention, as seen [here](https://github.com/dotnet/efcore/blob/6af71610366277d36698b69998cadfe6981c26b0/src/EFCore.Design/Properties/DesignStrings.resx#L169).

#### Can I use the Add Migration command to generate migrations for multiple DbContexts in the same project in EF Core?

Yes, you can use the `Add Migration` command to generate migrations for multiple `DbContexts` within the same project. However, there are some things you need to consider and steps to follow:

1. **Separate Folders**: Each `DbContext` should have its own separate migrations folder to avoid mixing up migrations from different contexts.
2. **Specifying the DbContext**: When adding or updating migrations, you'll need to specify which `DbContext` you are working with. You can do this using the `-Context` parameter in the Package Manager Console or the `--context` option when using the .NET CLI.

With Package Manager Console:

```
Add-Migration InitialCreate -Context BloggingContext
```

With .NET CLI:

```
dotnet ef migrations add InitialCreate --context BloggingContext
```
3. **Database Providers**: If the `DbContexts` use different database providers (e.g., one uses SQL Server and another uses PostgreSQL), ensure that the appropriate provider packages are installed, and ensure that the correct configuration is set in the `Startup.cs`, `Program.cs`, or wherever you configure your DbContexts.
4. **Unique Migration Names**: Make sure the migration names are unique across all `DbContexts`, especially if they share the same namespace, to avoid any conflicts.
5. **Running Migrations**: Similarly, when you want to update the database, specify the `DbContext` using the `-Context` parameter (in PMC) or `--context` option (in .NET CLI).

#### How can I specify the target database provider when using the Add Migration command in EF Core?

In Entity Framework Core (EF Core), the target database provider is determined by how you have set up your DbContext.

So you will need multiple context if you want to target different database provider.

You don't typically need to specify the database provider directly in the migrations command because EF Core infers it from the DbContext configuration. However, if you have multiple DbContexts targeting different providers, you'll need to specify which DbContext to use with the -Context parameter.

#### Is the EF Core Add Migration command supported in .NET Standard?

No, EF Core migrations aren't directly supported in .NET Standard due to the fundamental role of .NET Standard:

- .NET Standard isn't an actual implementation of the .NET runtime; instead, it's a specification of APIs that various .NET platforms must implement.
- Consequently, you don't run applications or commands against .NET Standard directly.

To utilize the `Add Migration` command, your project should be either `.NET` or `.NET Core`.

However, if your `DbContext` and entities reside within a .NET Standard library, you can reference that library from a `.NET` or `.NET Core` project and execute the migration commands from that referencing project.

#### What happens if I specify the same migration name multiple time in EF Core?

Each migration name must be unique in EF COre. If you try to add a migration with a name that already exists, you will receive an error similar to:

```
There is already a migration named '[MigrationName]' in the project.
```

https://github.com/dotnet/efcore/blob/6af71610366277d36698b69998cadfe6981c26b0/src/EFCore.Design/Properties/DesignStrings.resx#L189-L191

Where `[MigrationName]` will be the name of the migration you're trying to add. The exact wording of the error message might vary slightly based on the version of EF Core you're using, but the essence of the message remains consistent: it informs you of a naming conflict due to an existing migration with the same name.

#### Is it possible to generate an empty migration using the Add-Migration command?

Yes, it's possible to generate an empty migration using the `Add-Migration` command in EF Core. If you run the `Add-Migration` command with a name but without making any changes to your models, EF Core will create an empty migration.

This can be useful in scenarios where you might want to add custom SQL or perform manual operations on the database that aren't directly related to your entity model changes.

To create an empty migration:

1. Ensure there are no pending model changes.
2. Use the `Add-Migration` command and provide a name.

For example:

In the Package Manager Console (PMC):
```
Add-Migration EmptyMigration
```

Or using the .NET Command Line Interface (.NET CLI):
```
dotnet ef migrations add EmptyMigration
```

This will generate a migration with empty `Up` and `Down` methods, allowing you to add any custom operations you desire.




#### How to fix in EF Core: The name '{migrationName}' is used by an existing migration.

#### How to fix in EF Core: No changes have been made to the model since the last migration.

#### How to fix in EF Core: The namespace '{migrationsNamespace}' contains migrations for a different DbContext. This can result in conflicting migration names. It's recommend to put migrations for different DbContext classes into different namespaces.

#### How to fix in EF Core: The model snapshot and the backing model of the last migration are different. Continuing under the assumption that the last migration was deleted manually.

### How to fix in EF Core: Your target project '{assembly}' doesn't match your migrations assembly '{migrationsAssembly}'.

Full error message:
```
Your target project '{assembly}' doesn't match your migrations assembly '{migrationsAssembly}'. Either change your target project or change your migrations assembly.
Change your migrations assembly by using DbContextOptionsBuilder. E.g. options.UseSqlServer(connection, b =&gt; b.MigrationsAssembly("{assembly}")). By default, the migrations assembly is the assembly containing the DbContext.
Change your target project to the migrations project by using the Package Manager Console's Default project drop-down list, or by executing "dotnet ef" from the directory containing the migrations project.
```



#### How to fix in EF Core: The annotation '{annotationName}' was specified twice with potentially different values. Specifying the same annotation multiple times for different providers is no longer supported. Review the generated Migration to ensure it is correct and, if necessary, edit the Migration to fix any issues.

#### How to fix in EF Core: The provider '{provider}' is not a Relational provider and therefore cannot be used with Migrations.

#### How to fix in EF Core: You cannot add a migration with the name 'Migration'

#### How to fix in EF Core: The name you have chosen for the migration, '{name}', is the same as the context class name. Please choose a different name for your migration. Might we suggest 'InitialCreate' for your first migration?

#### How to fix in EF Core: Missing or incorrect connection string: The Add-Migration command requires a valid connection string to connect to the database. If the connection string is missing or incorrect, the command will fail. Ensure that the connection string is properly configured in your application's configuration file.
#### How to fix in EF Core: Missing or incorrect DbContext: The Add-Migration command needs to know which DbContext to use for generating the migration. If the DbContext is missing or not properly configured, the command will fail. Ensure that your DbContext is correctly implemented and registered in your application.

TODO:
Your startup project 'MyStartupProjectName' doesn't reference Microsoft.EntityFrameworkCore.Design. This package is required for the Entity Framework Core Tools to work. Ensure your startup project is correct, install the package, and try again.
Your target project 'MyStartupProjectName' doesn't match your migrations assembly 'Z.MyProjectName'. Either change your target project or change your migrations assembly.

/*
dotnet tool uninstall --global dotnet-ef --version 7.*
dotnet tool install --global dotnet-ef --version 7.*
https://stackoverflow.com/questions/57066856/command-dotnet-ef-not-found
*/

- **-Name <String>**: The name of your migration. For example, `Add-Migration AddingEFE` or `Add-Migration -Name AddingEFE` will create a migration named `AddingEFE`.
- **-OutputDir <String>**: The output directory relative to the project in which the the [migration file](/migrations/migration-file) will be created. For example, `Add-Migration AddingEFE -OutputDir MyMigrationDirectoryName` will add the migration file in the `MyMigrationDirectoryName` directory.
- **-Namespace <String>**: The namespace to use for the generated classes in the migration file. If omitted, the following namespace "{RootNamespace}.{OutputDirectory}.CompiledModels" will be used. For example, `Add-Migration AddingEFE -Namespace Z.MyMigrationNamespace` will add generated classes in the `Z.MyMigrationNamespace` namespace.
- **-Context <String>**: The context class to use to generate the migration file, it can be either only the `name` or the `fullname` including namespaces. For example, `Add-Migration AddingEFE -Context Z.MyContextName` will generate a migration for the `Z.MyContextName` context.
   - This parameter is optional (can be omitted), if your project contains only 1 context.
   - This parameter is mandatory, if your project contains more than 1 context.
- **-Project <String>**: The project that contains the context to use to generate the migration file. If omitted, the default project in the Package Manager Console is used. For example, `Add-Migration AddingEFE -Project Z.MyProjectName` will generate a migration for the context in the `Z.MyProjectName` project.
- **-StartupProject <String>**: The project that contains the configurations and settings. If omitted, the startup project of your solution is used.  For example, `Add-Migration AddingEFE -StartupProject Z.MyStartupProjectName` will generate a migration for the default project by using configuration and settings of the `Z.MyStartupProjectName` project.



Reference:
- https://learn.microsoft.com/en-us/ef/core/managing-schemas/migrations/managing?tabs=dotnet-core-cli
- https://learn.microsoft.com/en-us/ef/core/managing-schemas/migrations/?tabs=dotnet-core-cli
- https://www.entityframeworktutorial.net/efcore/entity-framework-core-migration.aspx
- https://www.infoworld.com/article/3691114/how-to-work-with-ef-core-migrations-in-aspnet-core.html
- https://www.csharptutorial.net/entity-framework-core-tutorial/ef-core-migrations/
- https://code-maze.com/migrations-and-seed-data-efcore/
- https://www.learnentityframeworkcore5.com/migrations-in-ef-core
- https://wakeupandcode.com/ef-core-migrations-in-asp-net-core/
- https://learn.microsoft.com/en-us/ef/core/cli/powershell#common-parameters
