---
title: Package Manager Console Commands
description: Commands in Entity Framework Core can be executed from within Visual Studio via the Package Manager Console Interface
canonical: /migrations/commands/pmc-commands
status: Published
lastmod: 2023-02-18
---

# EF Core Package Manager Console

The Package Manager Console is available within Visual Studio by going to **Tools** » **Nuget Package Manager**. 

If you want to use the Package Manager Console to execute migrations command, you need to ensure that the latest version of `Microsoft.EntityFrameworkCore.Tools` is added to your project.json file.

The commands that you execute to manage migrations from the Package Manager Console are largely the same as those for Entity Framework 6. The full range of commands can be found by typing `get-help entityframework` at the `PM` prompt:

```
PM> get-help entityframework

                     _/\__
               ---==/    \\
         ___  ___   |.    \|\
        | __|| __|  |  )   \\\
        | _| | _|   \_/ |  //|\\
        |___||_|       /   \\\/\\

TOPIC
    about_EntityFramework

SHORT DESCRIPTION
    Provides information about Entity Framework commands.

LONG DESCRIPTION
    This topic describes the Entity Framework commands. Entity Framework is Microsoft's recommended data access technology for new
    applications.

    The following Entity Framework cmdlets are included.

        Cmdlet                      Description
        --------------------------  ---------------------------------------------------
        Add-Migration		    Adds a new migration.

        Remove-Migration	    Removes the last migration.

        Scaffold-DbContext	    Scaffolds a DbContext and entity type classes for a specified database.

        Script-Migration	    Generates a SQL script from migrations.

        Update-Database		    Updates the database to a specified migration.

        Use-DbContext               Sets the default DbContext to use.

SEE ALSO
    Add-Migration
    Remove-Migration
    Scaffold-DbContext
    Script-Migration
    Update-Database
    Use-DbContext
```
You can get further help for specific commands by typing `get-help` followed by the command name. For example, the following output is generated as a result of typing `get-help add-migration`:
### Add-migration
```
NAME
    Add-Migration
    
SYNOPSIS
    Adds a new migration.
    
SYNTAX
    Add-Migration [-Name] <String> [-OutputDir <String>] [-Context <String>] [-Project <String>] [-StartupProject <String>] [-Environment <String>] [<CommonParameters>]
    
DESCRIPTION
    Adds a new migration.
```

#### Notes

The migration files will be placed in a folder name _Migrations_ by default. This will be created in the root of the project unless an alternative location is specified using the `-outputdir` switch. If you specify an alternative location with `outputdir`, the value will be rooted in the project directory. It should not be prefixed with a path separator (e.g. '/').


### Remove-Migration
```
NAME
    Remove-Migration
    
SYNOPSIS
    Removes the last migration.
    
    
SYNTAX
    Remove-Migration [-Force] [-Context <String>] [-Environment <String>] [-Project <String>] [-StartupProject <String>] [<CommonParameters>]
    
    
DESCRIPTION
    Removes the last migration.
    

RELATED LINKS
    Add-Migration
    about_EntityFrameworkCore 

REMARKS
    To see the examples, type: "get-help Remove-Migration -examples".
    For more information, type: "get-help Remove-Migration -detailed".
    For technical information, type: "get-help Remove-Migration -full".
```



### Scaffold-DbContext
```
NAME
    Scaffold-DbContext
    
SYNOPSIS
    Scaffolds a DbContext and entity types for a database.
    
    
SYNTAX
    Scaffold-DbContext [-Connection] <String> [-Provider] <String> [-OutputDir <String>] [-Context <String>] [-Schemas <String[]>] [-Tables <String[]>] [-DataAnnotations] [-Force] [-Environment <String>] [-Project <String>] 
    [-StartupProject <String>] [<CommonParameters>]
    
    
DESCRIPTION
    Scaffolds a DbContext and entity types for a database.
    

RELATED LINKS
    about_EntityFrameworkCore 

REMARKS
    To see the examples, type: "get-help Scaffold-DbContext -examples".
    For more information, type: "get-help Scaffold-DbContext -detailed".
    For technical information, type: "get-help Scaffold-DbContext -full".
```
### Script-Migration
```
NAME
    Script-Migration
    
SYNOPSIS
    Generates a SQL script from migrations.
    
    
SYNTAX
    Script-Migration [-From] <String> [-To] <String> [-Idempotent] [-Context <String>] [-Environment <String>] [-Project <String>] [-StartupProject <String>] [<CommonParameters>]
    
    Script-Migration [[-From] <String>] [-Idempotent] [-Context <String>] [-Environment <String>] [-Project <String>] [-StartupProject <String>] [<CommonParameters>]
    
    
DESCRIPTION
    Generates a SQL script from migrations.
    

RELATED LINKS
    Update-Database
    about_EntityFrameworkCore 

REMARKS
    To see the examples, type: "get-help Script-Migration -examples".
    For more information, type: "get-help Script-Migration -detailed".
    For technical information, type: "get-help Script-Migration -full".
```
#### Notes

The `script-migration` command will script all migrations, regardless of whether they have been applied or not. If you only want to script the most recent migration, the value to apply to the `-from` switch is the last migration that was applied. You can read `-from` as meaning "after", not "including".

### Update-Database
```
NAME
    Update-Database
    
SYNOPSIS
    Updates the database to a specified migration.
    
    
SYNTAX
    Update-Database [[-Migration] <String>] [-Context <String>] [-Environment <String>] [-Project <String>] [-StartupProject <String>] [<CommonParameters>]
    
    
DESCRIPTION
    Updates the database to a specified migration.
    

RELATED LINKS
    Script-Migration
    about_EntityFrameworkCore 

REMARKS
    To see the examples, type: "get-help Update-Database -examples".
    For more information, type: "get-help Update-Database -detailed".
    For technical information, type: "get-help Update-Database -full".
```
