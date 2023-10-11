---
id: 4176d137-b5f1-49fc-b1ce-d2d23be7ddb0
position: 1
title: How to fix in EF Core: The term 'Remove-Migration' isn't recognized as a valid command, function, script file, or operable program.
---

<div class="h9">Exception Message:</div>

```
PM> Remove-Migration
Remove-Migration : The term 'Remove-Migration' is not recognized as the name of a cmdlet, function, script file, or operable program. Check the spelling of the name, or if a path was included, verify that the path is correct and try again.
At line:1 char:1
+ Remove-Migration
+ ~~~~~~~~~~~~~~~~
    + CategoryInfo          : ObjectNotFound: (Remove-Migration:String) [], CommandNotFoundException
    + FullyQualifiedErrorId : CommandNotFoundException
```

<div class="image-outer"><img src="https://www.learnentityframeworkcore.com/images/efcore/migrations/remove-migration/troubleshooting-the-term-remove-migration-isn-t-recognized-as-a-valid-command.png" loading="lazy" alt="Exception - The migration has already been applied to the database"></div>

<div class="h9">Cause:</div>

This error arise when the [Microsoft.EntityFrameworkCore.Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools) package isn't referenced in your migrations project.

<div class="h9">Solution:</div>

Ensure the [Microsoft.EntityFrameworkCore.Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools) package is installed or referenced in your migrations project.

You can install it via NuGet Package Manager:

```
Install-Package Microsoft.EntityFrameworkCore.Tools
```

Or using .NET CLI:

```
dotnet add package Microsoft.EntityFrameworkCore.Tools
```