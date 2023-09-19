---
id: 8c04b06d-628b-4a49-9738-300f07a369c2
position: 1
title: How to fix in EF Core: The term 'Add-Migration' is not recognized as the name of a cmdlet, function, script file, or operable program.
---

<div class="h9">Exception Message:</div>

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

<div class="image-outer"><img src="/images/efcore/migrations/add-migration/troubleshooting-the-term-add-migration-is-not-recognized-as-the-name-of-a-cmdlet.png" loading="lazy" alt="Exception - The term Add Migration is not recognized"></div>

<div class="h9">Cause:</div>

This error arises when the [Microsoft.EntityFrameworkCore.Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools) package is not referenced in your `Migrations Project`.

<div class="h9">Solution:</div>

Make sure you have installed or referenced the [Microsoft.EntityFrameworkCore.Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools) package in your `Migrations Project`. 

You can add this package via the NuGet Package Manager with:

```
Install-Package Microsoft.EntityFrameworkCore.Tools
```

Or through the .NET CLI with:

```
dotnet add package Microsoft.EntityFrameworkCore.Tools
```
