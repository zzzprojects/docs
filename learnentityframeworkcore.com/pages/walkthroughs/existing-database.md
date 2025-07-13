---
title: Generating a model from an existing database
description: Database-first has been deprecated in Entity Framework Core in favor of a Code first approach with an existing database.
canonical: /walkthroughs/existing-database
status: Published
lastmod: 2025-07-13
---

# EF Core Database First

Previous versions of Entity Framework support a Database-First approach to development. In this approach, you reverse-engineer a model from an existing database, resulting in the generation of an EDMX file that contains the model definition and mapping information. Tooling support for the EDMX file was dropped in Entity Framework Core in favor of using commands to reverse-engineer class files for the model from an existing database schema. This approach is known as Code First to an existing database.

## Command Line Interface

The following example illustrates how to use code first to generate a model from a SQL Server database in a new console application using the CLI tools.

First, create a folder for the project:

```cmd
> mkdir EFCoreScaffoldExample
```
Then navigate to it:

```cmd
> cd EFCoreScaffoldExample
```
Then create a new project:
```cmd
> dotnet new console
```
Add the Entity Framework Core and Tools packages to the project:
```cmd
> dotnet add package Microsoft.EntityFrameworkCore.SqlServer
> dotnet add package Microsoft.EntityFrameworkCore.Design
```

The first package is the EF Core provider for SQL Server. The second package contains the Entity Framework Core commands. Both of these packages are required for any Entity Framework Core application that targets SQL Server. 


Test to see if [ef commands](/migrations/commands/cli-commands) are available to you:

```cmd
dotnet ef -h
```

This should result in the initial help for the EF tools being displayed:

![EF commands Help](/images/16-02-2017-08-30-50.png)

If you get errors like this:

> Could not execute because the specified command or file was not found.
> Possible reasons for this include:  
>  &#42; You misspelled a built-in dotnet command.  
>  &#42; You intended to execute a .NET Core program, but dotnet-ef does not exist.  
>  &#42; You intended to run a global tool, but a dotnet-prefixed executable with this name could not be found on the PATH.

try installing the EF Core tool by executing the following command:

```cmd
dotnet tool install --global dotnet-ef
```

You use the [DbContext Scaffold](/migrations/commands/cli-commands#dbcontext-scaffold) command to generate the model. The command has two required arguments - a connection string and a provider. The connection string will depend on your environment and database provider. The provider argument is the Entity Framework provider for your chosen database. This example uses the [AdventureWorks sample database](https://github.com/Microsoft/sql-server-samples/releases/tag/adventureworks) for the SQL Server provided by Microsoft.
```cmd
> dotnet ef dbcontext scaffold "Server=.\;Database=AdventureWorksLT2012;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -o Model
```
Once you have executed the command, you will see that a folder named _Model_ has been created in the project folder, containing a collection of class files representing the entities in addition to a file for the `DbContext` class:

![Scaffolded Model in Visual Studio Code](/images/23-02-2017-09-47-16.png)

The `-o` option (or `--output-dir`) specifies the directory where the class files will be generated. If it is omitted, the class files will be generated in the project directory (where the _.csproj_ file is located).

The `DbContext` class will take the name of the database plus "Context", You can override this using the `-c` or `--context` option e.g.

```cmd
> dotnet ef dbcontext scaffold "Server=.\;Database=AdventureWorksLT2012;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -o Model -c "AdventureContext"
```
### Model Configuration

The resulting model is configured using the [Fluent API](/configuration/fluent-api) by default, which is the recommended approach. The configurations are placed in the generated context's `OnModelCreating` method.  However, if you prefer to use [Data Annotations](/configuration/data-annotation-attributes) for configuration, you can use the `-d` or `--data-annotations` switch:

```cmd
dotnet ef dbcontext scaffold "Server=.\;Database=AdventureWorksLT2012;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -d 
```
Since Data Annotations only cover a subset of configuration options, you are likely to find that the Fluent API has also been used to configure the model.

## Updating the Model

The recommended approach to keeping changes to the database in sync with the generated model is to use [migrations](/migrations) i.e. to make the changes to the model first, and then use tools to generate code that propagates the modifications to the database. However, depending on your circumstances, this may not always be an option. If you need to re-scaffold the model after database schema changes have been made, you can do so by specifying the `-f` or `--force` option e.g.:

```cmd
dotnet ef dbcontext scaffold "Server=.\;Database=AdventureWorksLT2012;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer --force
```
All of the class files will be overwritten, which means that any amendments that you might have made to them e.g. adding attributes or additional members, will be lost. You can mitigate this by opting to use the [Fluent API for configuration and using separate configuration classes](/configuration/fluent-api#separate-configuration-classes). In addition, you can use partial classes to declare additional properties that don't map to columns in the database tables.


## Visual Studio

If you are working with Visual Studio, you can use the [Package Manager Console commands](/migrations/commands/pmc-commands#scaffold-dbcontext) to generate the code files for the model. The equivalent command to the last CLI command just above is:

```cmd
PM> Scaffold-DbContext "Server=.\;Database=AdventureWorksLT2012;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -OutputDir Model -Context "AdventureContext" -DataAnnotations
```
## Migrations

In previous versions of Entity Framework, it was possible, once the model has been created, to add a migration that did not affect the schema of the existing database using the `-ignorechanges` option. This option does not exist in Entity Framework Core, so the workaround is to create a first migration and then delete or comment out the contents of the `Up` method before applying the migration to the database. This will result in a model and a database schema that match. 
