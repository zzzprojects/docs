---
title: Commands in Entity Framework Core
description: Several management tasks including migrations are managed in Entity Framework Core by executing commands.
canonical: /migrations/commands
status: Published
lastmod: 2025-07-13
---

# EF Core Commands

Entity Framework Core Migration commands can be executed from the [command line](/migrations/commands/cli-commands) or from within Visual Studio via the [Package Manager Console](/migrations/commands/pmc-commands) (PMC). The EF commands package also provides other utilities such as reverse engineering an existing database to scaffold a model comprising POCO class files.

Commands are made available to the project through the inclusion of the `Microsoft.EntityFrameworkCore.Tools` package. You can install this in your project by executing the following command from within the PMC:

`PM> Install-Package Microsoft.EntityFrameworkCore.Tools`

or by adding the package to your _project.json_ file:

```json
"dependencies": {
    "Microsoft.EntityFrameworkCore.Tools": "1.0.0-preview2-final",
    ...
```

If you use the command line to execute commands, you must do so from within the `DbContext`'s project directory that contains the _project.json_ file (usually `src`). You do this by opening a command prompt and using the [`chdir` or `cd`](https://technet.microsoft.com/en-gb/library/bb490875.aspx) command to change the current directory. 

If you are using Visual Studio, you will find the PMC by going to **Tools** » **NuGet Package Manager** » **Package Manager Console**. You enter commands at the `PM>` prompt. When executing commands, you should ensure that the Default Project selector shows the project containing the `DbContext`

![alt text](/images/15-08-2016-08-51-37.png)

Otherwise, you need to provide a value for the project directory option where required indicating the project where the `DbContext` resides.

You can also execute CLI commands from within the PMC. Just as with the command prompt approach, you must navigate to the DbContext project's directory containing the _project.json_ file.



<!--https://github.com/NuGet/Home/issues/3023  Says that the dependencies entry makes PMC commands available and the tool's entry makes .NET CLI commands available-->
