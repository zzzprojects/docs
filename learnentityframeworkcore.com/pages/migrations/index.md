---
title: Entity Framework Core Migrations
description: The migrations feature in Entity Framework Core enables you to make changes to your model and then propagate those changes to your database schema.
canonical: /migrations
lib: src/EFCore.Relational/Migrations
status: Published
lastmod: 2023-08-10
---

# EF Core Migrations

EF Core Migrations is a feature of EF Core that enables developers to evolve the database schema over time in a versioned manner as the application evolves. 

 - With migrations, you can easily apply or revert database changes as your application evolves, without losing existing data. 
 - EF Core Migrations automatically generates and executes the necessary SQL scripts to update the database schema, so you don't have to write manual scripts or worry about data loss.

## Migration Command

### Package Manager Console

| Package Manager Console | Description |
| --- | --- | --- |
| `add-migration [name]`  | Create a new migration with the specific migration name. |
| `remove-migration` | Remove the latest migration. |
| `update-database` | Update the database to the latest migration. |
| `update-database [name]` | Update the database to a specific migration name point. |
| `get-migrations` | Lists all available migrations. |
| `script-migration` | Generates a SQL script for all migrations. |
| `drop-database` | Drop the database. |

### Command Line

| Command Line | Description |
| --- | --- |
| `dotnet ef migrations add [name]` | Create a new migration with the specific migration name. |
| `dotnet ef migrations remove` | Remove the latest migration. |
| `dotnet ef database update` | Update the database to the latest migration. |
| `dotnet ef database update [name]` | Update the database to a specific migration name point. |
| `dotnet ef migrations list` | Lists all available migrations. |
| `dotnet ef migrations script` | Generates a SQL script for all migrations. |
| `dotnet ef database drop` | Drop the database. |

## Steps for Migrations

Here are the steps for using EF Core Migrations in a .NET project:

 - To use migrations in EF Core, you start by defining your database schema using code, such as POCO classes and `DbContext`. 
 - Then you run the EF Core CLI or Package Manager Console commands to add a new migration, which generates code that represents the changes you have made to your database schema. 
 - Finally, you run the command to apply the migrations, which updates the database schema.

## Why you need migrations

EF Core Migrations provides a flexible and convenient way to manage database changes, especially in team development environments where multiple developers might be making changes to the same database schema. EF Core migrations are needed for several reasons:

 1. **Schema evolution:** As your application evolves, so does the database schema. EF Core migrations enable you to track and manage these changes to the schema over time.
 2. **Easy database management:** With EF Core migrations, you can manage database changes in a versioned manner, making it easier to manage the database as your application evolves.
 3. **Automated database creation:** You can use EF Core migrations to create the database schema automatically, eliminating the need for manual SQL scripts or other manual database management processes.
 4. **Consistent database state:** EF Core migrations ensure that all instances of the database are in a consistent state, reducing the likelihood of errors and inconsistencies in the database.
 5. **Improved collaboration:** EF Core migrations make it easier for teams to work together on a project, as each developer can work with a local copy of the database that is in sync with the latest schema changes.

Migrations are enabled by default in EF Core. They are managed by executing [commands](/migrations/commands). If you have Visual Studio, you can use the [Package Manager Console (PMC)](/migrations/commands/pmc-commands) to manage migrations. Alternatively, you can use a [command line tool to execute Entity Framework CLI commands](/migrations/commands/cli-commands) to create a migration.

## Creating a Migration

The following command creates a migration:

```
[Command Line]
dotnet ef migrations add <name of migration>

[Package Manager console]
add-migration <name of migration>
```

When you create a migration, the framework compares the current state of the model with the previous migration if one exists and generates a file containing a class inheriting from `Microsoft.EntityFrameworkCore.Migrations.Migration` featuring an `Up` and a `Down` method. 

 - The class is given the same name as you specified for the migration. 
 - The file name itself is the name of the migration prefixed with a timestamp.
 - The `Up` method contains C# code that applies any changes made to the model to the schema of the database since the last migration was generated. 
 - The `Down` method reverses those changes, restoring the database to the state of the previous migration. 
 - A [ModelSnapshot file](/migrations/model-snapshot) is also created or updated, depending on whether one previously existed.

## Removing A Migration

The following command removes a migration:

```
[Command Line]
dotnet ef migrations remove

[Package Manager Console]
remove-migration
```

You will use this command to remove the latest migration. This will remove the class file that was generated for the latest migration and it will also revert the [ModelSnapshot file](/migrations/model-snapshot) to the state of the previous migration. If there are no pending migrations, an exception will be raised. If you want to remove a migration that has been committed, you must reverse the migration first (see below).
     
 - You should always use commands to remove a migration instead of simply deleting the migration code file, otherwise, the snapshot and migrations will fall out of sync with each other. 
 - Then future migrations will be based on an incorrect model. Nevertheless, the `remove`  command will recognize if migration files have been deleted and will revert the snapshot accordingly.
 - If you need to remove a migration that was generated before the most recent one, you must remove all subsequent migrations first, then adjust the model, and then create a new migration to cater to the changes.

## EF Core Update Database

The following command results in the `Up` method of the migration class being executed with any changes applied to the database:

```
[Command line]
dotnet ef database update

[Package Manager Console]
update-database
```
Unless otherwise specified, all pending migrations will be applied. If this is the first migration, a table will be added to the database called `__EFMigrationsHistory`. It is used to store the name of this and each subsequent migration as they are applied to the database. 

## Reversing A Migration

To reverse a migration, you pass the name of a target migration to the `update` command. The target migration is the point at which you want to restore the database. 

For example, if your first migration is named "Create", and you would like to restore the database to remove all changes made by subsequent migrations, you pass "Create" to the update command:
```
[Command line]
dotnet ef database update Create

[Package Manager Console]
update-database Create
```
This action will result in the `Down` method in all subsequent migrations being executed. 

 - Any migrations that have been applied to the database after the target migration will have their entries removed from the `__EFMigrationsHistory` table in the database. 
 - It will not remove subsequent migrations from the Migrations folder, or alter the ModelSnapshot file. 
 - You should use the `remove` command to achieve this rather than manually deleting the migrations files.

## Applying A Migration To A Remote Database

At some stage, you will need to deploy one or more migrations against another database, whether that is a test database or a live production database. Currently, the easiest way to accomplish that is to execute SQL scripts against the target database. You can generate the required script via the `script` command:

```
[Command Line]
dotnet ef migrations script

[Package Manager Console]
script-migration
```

By default, all migrations will be included. You can specify a range of migrations to include by using the `-to` and `-from` options.

<!--https://github.com/aspnet/EntityFramework/issues/6228-->

## Executing Custom SQL

Sometimes, you may want to execute custom SQL at the same time as the migration is applied to the database. You may need to do this because your database provider doesn't support something, or because the operation you want to perform is not related to the migration. Or you might want to add a new column to a table and then populate it with data.

In these instances, you can use the `MigrationBuilder.Sql` method. This should be placed in the generated `Up` method at the point where you want the SQL to be executed. In the example below, the method is called before the first table is created:

```csharp
public partial class CreateDatabase : Migration
{
    protected override void Up(MigrationBuilder migrationBuilder)
    {
        migrationBuilder.Sql("Some custom SQL statement");

        migrationBuilder.CreateTable(
            name: "Authors",
            columns: table => new
            {
                AuthorId = table.Column<int>(nullable: false)
                    .Annotation("SqlServer:ValueGenerationStrategy", SqlServerValueGenerationStrategy.IdentityColumn),
                FirstName = table.Column<string>(nullable: true),
                LastName = table.Column<string>(nullable: true)
            },
            constraints: table =>
            {
                table.PrimaryKey("PK_Authors", x => x.AuthorId);
            });
    }
}
```            


If you need to reverse the custom SQL operation if the migration is applied, you would use the `Sql` method to execute the appropriate SQL in the `Down` method.

## Targeting Multiple Providers

Migrations are generated for a specific provider. Although a lot of work has been done to make migrations as provider-agnostic as possible, there will inevitably be cases where a migration generated for one provider cannot be used against another. 

 - Annotations are used for provider-specific migrations operations, and if they don't apply to the current provider, they are ignored. 
 - You can therefore "overload" the migration with as many annotations as you need safe in the knowledge that the current provider will only apply the ones that relate to it:

```csharp
public partial class CreateDatabase : Migration
{
    protected override void Up(MigrationBuilder migrationBuilder)
    {
        migrationBuilder.CreateTable(
            name: "Authors",
            columns: table => new
            {
                AuthorId = table.Column<int>(nullable: false)
                    .Annotation("SqlServer:ValueGenerationStrategy", SqlServerValueGenerationStrategy.IdentityColumn)
                    .Annotation("Sqlite:Autoincrement", true),
                FirstName = table.Column<string>(nullable: true),
                LastName = table.Column<string>(nullable: true)
            },
```

The `Migration` class exposes an `ActiveProvider` property that gets the name of the current database provider being used. This can help generate conditional code enabling a single migration to target multiple providers:

```csharp
public partial class CreateDatabase : Migration
{
    protected override void Up(MigrationBuilder migrationBuilder)
    {

        if(ActiveProvider == "Microsoft.EntityFrameworkCore.SqlServer")
        {
            // do something SQL Server - specific
        }
        if(ActiveProvider == "Microsoft.EntityFrameworkCore.Sqlite")
        {
            // do something SqLite - specific
        }

        migrationBuilder.CreateTable(
            name: "Authors",
            columns: table => new
            {
                AuthorId = table.Column<int>(nullable: false)
                    .Annotation("SqlServer:ValueGenerationStrategy", SqlServerValueGenerationStrategy.IdentityColumn),
                FirstName = table.Column<string>(nullable: true),
                LastName = table.Column<string>(nullable: true)
            },
            constraints: table =>
            {
                table.PrimaryKey("PK_Authors", x => x.AuthorId);
            });
``` 

## Setting The Migration Timeout

Your migration might include a long-running task that exceeds the default command timeout value (30 seconds for SQL Server), resulting in an exception being raised. You can set a different value for the timeout at the `DbContext` level, but this will apply to all operations undertaken by the `DbContext` which may not be desirable.  

The `IDesignTimeDbContextFactory` interface was introduced to enable you to change the behavior of your context when it is being created by tooling at design time such as happens with Migrations.

To use this approach, create a separate class in your project that implements the `IDesignTimeDbContextFactory` interface and use the `DbContextoptionsBuilder` to configure the behaviour you want - in this case, setting the command timeout value to 600 seconds (10 minutes):

```csharp
using Microsoft.EntityFrameworkCore;
using Microsoft.EntityFrameworkCore.Design;

namespace EFCoreSample.Model
{
    public class SampleContextFactory : IDesignTimeDbContextFactory<SampleContext>
    {
        public SampleContext CreateDbContext(string[] args)
        {
            var optionsBuilder = new DbContextOptionsBuilder<SampleContext>();
            optionsBuilder.UseSqlServer(@"Server=.\;Database=db;Trusted_Connection=True;", opts => opts.CommandTimeout((int)TimeSpan.FromMinutes(10).TotalSeconds));

            return new SampleContext(optionsBuilder.Options);
        }
    }
}
```

Make sure that your existing `DbContext` has a constructor that takes a `DbContextOptions` object as a parameter:

```csharp
public AdventureContext(DbContextOptions options) : base(options){}
```

When the tooling runs the migration, it looks first for a class that implements `IDesignTimeDbContextFactory` and if found, will use that for configuring the context. Runtime behavior is not affected by any configuration settings in the factory class.

## Using the 'Migrate' method

The `context.Database.Migrate()` or `context.Database.MigrateAsync()` method will apply pending migrations for the context to the database. 

```csharp
var context = new MyContext();
context.Database.Migrate();
```

The method is helpful if you want to apply your migrations automatically at runtime. When your application start by example.