---
title: How to get Entity Framework Core
description: Entity Framework Core is available as a NuGet Package that can be added to your project in several ways depending on the project type and the tools available to you
canonical: /efcore/how-to-get
status: Published
lastmod: 2025-07-13
---

# How to get EF Core - NuGet Package

EF Core is available as a NuGet Package that can be added to your project in many ways depending on the project type and the tools available to you.

## Visual Studio Package Manager

Users of Visual Studio can install Entity Framework Core via one of the package management tool options regardless of the project type (.NET Core or the full .NET Framework): the NuGet Package Manager UI; or the Package Manager Console.

### NuGet Package Manager UI

1. Go to **Tools** &raquo; **NuGet Package Manager** &raquo; **Manage NuGet Packages For Solution**  
![NuGet Package Manager UI](/images/23-11-2016-13-58-47.png)

2. Ensure that **Browse** is selected and type "entityframeworkcore" into the search box  
![Browse packages](/images/24-11-2016-08-53-33.png)

3. Click on the provider that you want to install. SQL Server is selected in this case.  
![Select package](/images/24-11-2016-08-56-16.png)

4. Check the project that you want to install the package into, then click Install  
![Install Package](/images/24-11-2016-08-57-21.png)

5. Review the changes that are about to be made to your project (unless you have previously ticked the box to prevent this dialog from appearing)  
![Review Changes](/images/24-11-2016-09-02-43.png)

6. Finally, accept the terms of the various licenses associated with the packages that are about to be installed  
![Accept Licenses](/images/24-11-2016-09-03-29.png)  

### Package Manager Console
1. Go to **Tools** &raquo; **NuGet Package Manager** &raquo; **Package Manager Console**  
![Package Manager Console](/images/23-11-2016-14-08-28.png)

2. Ensure that the correct project is selected in the "Default Project" dropdown, and type `install-package microsoft.entityframeworkcore.sqlserver` to install the SQL Server provider.  
 ![Install Package](/images/23-11-2016-14-14-28.png)

3. Hit return, and the installation process should start.

## Command Line Tools

You can use the .NET Core command line tools to install Entity Framework Core. Once you have created your project, navigate to the folder containing the _.csproj_ (or _project.json_) file and execute the following command:

```cmd
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
```
You also need the Entity Framework Core tools if you want to make use of EF commands for migrations, scaffolding, etc, so type the following command:
```cmd
dotnet add package Microsoft.EntityFrameworkCore.Tools --version 1.1.0-msbuild3-final 
```

## Project.json

The _project.json_ file has been deprecated in favor of a _.csproj_ file, but for older .NET Core projects on any platform, you can modify the _project.json_ file to install Entity Framework Core. The following example shows a basic _project.json_ file amended to include the 1.1.0 version of the SQL Server provider:

```json
{
  "version": "1.0.0-*",
  "buildOptions": {
    "emitEntryPoint": true
  },
  "dependencies": {
    "Microsoft.Extensions.Configuration": "1.0.0",
    "Microsoft.Extensions.Configuration.FileExtensions": "1.0.0",
    "Microsoft.Extensions.Configuration.Json": "1.0.0",
    "Microsoft.EntityFrameworkCore.SqlServer": "1.1.0",
    "Microsoft.EntityFrameworkCore.Tools": "1.1.0-msbuild3-final",
    "Microsoft.NETCore.App": {
      "type": "platform",
      "version": "1.0.0"
    }
  },
  "frameworks": {
    "netcoreapp1.0": {
      "imports": "dnxcore50"
    }
  },
  "tools": {
    "Microsoft.EntityFrameworkCore.Tools": "1.1.0-preview4"
  }
}
```
A reference to the tooling is also included in both the `dependencies` section and the `tools` section. These are required if you want to run [commands](/migrations/commands) for scaffolding or migrations.

If you choose this approach to installing Entity Framework Core while using Visual Studio, packages are automatically restored when you save the changes to the _project.json_ file. Otherwise, you may need to manually restore packages, depending on the IDE that you are using and its capabilities. To manually restore the package, use a command prompt to navigate to the folder that houses the _project.json_ file and type `dotnet restore`. This assumes that you have the [.NET Core SDK](https://www.microsoft.com/net/core) installed.


