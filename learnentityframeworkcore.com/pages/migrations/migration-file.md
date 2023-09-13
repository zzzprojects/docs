---
title: ???
description: ???
canonical: /migrations/add-migration
status: Unpublished
lastmod: 2023-09-01
thumbnai: /images/efcore/migrations/add-migration/thumbnail-ef-core-add-migration.png
localhost: https://localhost:5005/migrations/add-migration
---

# EF Core Migration File

[Image: MigrationFile]

When using the [Add-Migration](#) commands, 3 files are generated:

EF Core's migration file is an integral tool for those utilizing the [code-first approach](#). 

Generated with the [add-migration](#) command and removed using the [remove-migration](#) command, this file acts as a blueprint for database schema modifications.

To either apply or revert to a specific migration, the [database-update](#) command comes into play.

Within the migration file, you'll encounter two pivotal methods:

- [Up Method](#): Defines the alterations to be executed on the database during migration application.
- [Down Method](#): Details the steps to revert the database to its state prior to the current migration.

# EF Core Migration File

[Image:MigrationFile]

The migration file is a file created by EF Core for people using [code-first approach](#). 

The migration file is created by using the [add-migration](#) command, or removed using the [remove-migration](#) command.

You can also use the [database-update](#) command to either move or revert the database to a specific migration. 

The file typically contains two primary method:

- [Up Method](#): This method contains the changes to apply to the database when applying a migration.
- [Down Method](#): This method contains the changes to apply to the database when reverting a migration.

## What is the migration file?
A migration file is an automatically generated script by Entity Framework Core, detailing changes to be made to the database schema.

## Up Method

// TODO: Test really what was added

The `Up` method contains instructions to apply the desired changes, transitioning the database to a newer state.

For instance, consider an `Email` property has been added to the `Customer` entity. When using the [Add-Migration](#) command, a new migration file will be created with the `Up` method that would encapsulate the code to introduce this new column to the database. Here is an example of what the `Up` method could contains:

```csharp
public override void Up()
{
    AddColumn("dbo.Customers", "Email", c => c.String(nullable: false, maxLength: 255));
}
``` 

In this example, the `AddColumn` method specifies the addition of an "Email" column to the "Customers" table. The new column is of string type, cannot be null, and has a maximum length of 255 characters.

## Down Method

The `Down` method contains instructions to reverse the actions of the `Up` method, reverting the database back to its previous state.

For instance, referring back to our previous example where the `Email` property was added, to revert this change, we would need to remove the column from our database. Here is an example of what the `Down` method could contains:

```csharp
public override void Down()
{
    DropColumn("dbo.Customers", "Email");
}
```

In this example, the `DropColumn` method indicates that the "Email" column will be removed from the "Customers" table.

## Available Method

### MigrationBuilder Methods

https://github.com/dotnet/efcore/blob/release/8.0/src/EFCore.Relational/Migrations/MigrationBuilder.cs

#### Add

- **AddColumn<T>**:  Add a column to a table. The `T` reprensent the column clr type.
- **AddCheckConstraint**: Add a constraint to a table.
- **AddForeignKey**: Add a foreign key to a table.
- **AddPrimaryKey**: Add a primary key to a table.
- **AddUniqueConstraint**: Add a unique constraint to a table.

#### Alter

- **AlterColumn<T>**: Alter a column of a table. The `T` represent the column clr type. Returns a [AlterOperationBuilder](#alteroperationbuilder) builder.
- **AlterDatabase**: Alter a database. Returns a [AlterOperationBuilder](#alteroperationbuilder) builder.
- **AlterSequence**: Alter a sequence of a table. Returns a [AlterOperationBuilder](#alteroperationbuilder) builder.
- **AlterTable**: Alter a table of a database. Returns a [AlterOperationBuilder](#alteroperationbuilder) builder.


#### Create

- **CreateIndex**: Create an index to a table.
- **CreateSequence**: Create a sequence to a table.
- **CreateSequence<T>**: Create a sequence to a table. The `T` represent the sequence clr type.
- **CreateCheckConstraint**: **Obsolete**: Use `AddCheckConstraint` instead. Create a check constraint to a table. 
- **CreateTable<TColumns>**: Create a table to a database. The `TColumns` is usually a anonymous type that contains type for every columns. Returns a [CreateTableBuilder](#CreateTableBuilder) builder.
- A [CreateTableBuilder](#CreateTableBuilder) is returned. 

#### Drop

- **DropColumn**: Drop a column from a table.
- **DropForeignKey**: Drop a foreign key from a table.
- **DropIndex**: Drop an index from a table.
- **DropPrimaryKey**: Drop a primary key from a table.
- **DropSchema**: Drop a schema from a database.
- **DropSequence**: Drop a sequence from a table.
- **DropCheckConstraint**: Drop a check constraint from a table.
- **DropTable**: Drop a table from a database.
- **DropUniqueConstraint**: Drop a unique constraint from a table.

#### Rename

- **RenameColumn**: Rename a column from a table.
- **RenameIndex**: Rename an index from a table.
- **RenameSequence**: Rename an index from a table.
- **RenameTable**: Rename a table from a database.

#### Crud

- **InsertData**: Insert one or multiple data into a table.
- **DeleteData**: Delete one or multiple data from a table.
- **UpdateData**: Update one or multiple data from a table.

#### Misc

- **EnsureSchema**: Ensure a schema exists from a database. The schema will only be created if not already exists.
- **RestartSequence**: Restart a sequence from a table to a specific value.

#### Sql

-**Sql**: Execute any SQL statement to the database. The SQL statement could be an `insert`, `update`, `delete`, or any other sql that is required for your `Up` or `Down` method.

### Specific Builder Methods

#### OperationBuilder

This builder can be found in the [OperationBuilder.cs](https://github.com/dotnet/efcore/blob/release/8.0/src/EFCore.Relational/Migrations/Operations/Builders/OperationBuilder.cs) file on GitHub.

This builder is returned by all any other methods not returned an `AlterOperationBuilder` or `CreateTableBuilder` builder.

It contains the following methods:

- **Annotation**: Annotates the operation with the given name/value pair.

#### AlterOperationBuilder

This builder can be found in the [AlterOperationBuilder.cs](https://github.com/dotnet/efcore/blob/release/8.0/src/EFCore.Relational/Migrations/Operations/Builders/AlterOperationBuilder.cs) file on GitHub.

This builder is returned when calling one of the following methods:

- `AlterColumn<T>`
- `AlterDatabase`
- `AlterSequence`
- `AlterTable`

It contains the following methods:

- **Annotation**: Annotates the operation with the given name/value pair.
- **OldAnnotation**: Annotates the operation with the given name/value pair as it was present before the alter.

#### CreateTableBuilder

This builder can be found in the [CreateTableBuilder.cs](https://github.com/dotnet/efcore/blob/release/8.0/src/EFCore.Relational/Migrations/Operations/Builders/CreateTableBuilder.cs) file on GitHub.

This builder is returned when calling one of the following methods:

- `CreateTable<TColumns>`

It contains the following methods:

- **Annotation**: Annotates the operation with the given name/value pair.
- **ForeignKey**: Specify a foreign key to the table.
- **PrimaryKey**: Specify a primary key to the table.
- **CheckConstraint**: Specify a check constraint to the table.
- **UniqueConstraint**: Specify a unique constraint to the table.

### Provider Specific

#### MySQL Specific (MySql.Data.EntityFrameworkCore)

Unfortunately, the code for [MySql.Data.EntityFrameworkCore](https://www.nuget.org/packages/MySql.Data.EntityFrameworkCore) is not open source. We were unsuccessful in finding the source of `MySql` extensions for migration when decompiling the DLL.

However, several sources point to two specific extensions for migrations. These were found in the `MySQLMigrationsSqlGenerator.cs` file when decompiling and are also mentioned in the [MySQL Connector](https://dev.mysql.com/doc/connector-net/en/connector-net-ref-efcore.html) documentation.

- `IsMySql`: Returns `true` if the current active provider for the migration is `MySQL`.
- `CharSet`: Sets the charset for an entity property.
- `Collation`: Sets the collation for an entity property.

#### MySQL Specific (Pomelo.EntityFrameworkCore.MySql)

You can find specific [Pomelo.EntityFrameworkCore.MySql](https://www.nuget.org/packages/Pomelo.EntityFrameworkCore.MySql) extensions for migration in the [MySqlMigrationBuilderExtensions.cs](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MySql/blob/master/src/EFCore.MySql/Extensions/MySqlMigrationBuilderExtensions.cs) file on GitHub. As of **2023**, it contains the following methods:

- **IsMySql**: Returns `true` if the current active provider for the migration is `MySQL`.
- **DropPrimaryKey**: Drops a specific primary key for a table.
- **DropUniqueConstraint**: Drops a specific unique constraint for a table.

#### PostgreSQL Specific

You can find specific `PostgreSQL` extensions for migration in the [NpgsqlMigrationBuilderExtensions.cs](https://github.com/npgsql/efcore.pg/blob/main/src/EFCore.PG/Extensions/NpgsqlMigrationBuilderExtensions.cs) file on GitHub. As of **2023**, it contains the following methods:

- **IsNpgsql**: Returns `true` if the current active provider for the migration is `PostgreSQL`.
- **EnsurePostgresExtension](https://github.com/npgsql/efcore.pg/blob/9b84f9e8dee6258c987e04232c67a79549b6e2cb/src/EFCore.PG/Extensions/NpgsqlMigrationBuilderExtensions.cs#L27): Ensure the `PostgreSQL` extension is added. The extension will be added only if it doesn't already exist.
- **CreatePostgresExtension**: **Obsolete**; Use `EnsurePostgresExtension` instead.
- **DropPostgresExtension**: **Obsolete**; Doesn't do anything anymore. Before, it removed an added PostgreSQL extension.

#### SQLite Specific

You can find specific `SQLite` extensions for migration in the [SqliteMigrationBuilderExtensions.cs](https://github.com/dotnet/efcore/blob/release/8.0/src/EFCore.Sqlite.Core/Extensions/SqliteMigrationBuilderExtensions.cs) file on GitHub. As of **2023**, it contains the following methods:

- **IsSqlite**: Returns `true` if the current active provider for the migration is `SQLite`.

#### SQL Server Specific

You can find specific `SQL Server` extensions for migration in the [SqlServerMigrationBuilderExtensions.cs](https://github.com/dotnet/efcore/blob/release/8.0/src/EFCore.SqlServer/Extensions/SqlServerMigrationBuilderExtensions.cs) file on GitHub. As of **2023**, it contains the following methods:

- **IsSqlServer**: Returns `true` if the current active provider for the migration is `SQL Server` or `Azure SQL databases`.