---
id: aa7177a3-056d-431e-8b9e-a2fb87b19526
position: 2
title: How to fix in EF Core: Your startup project '[StartupProjectName]' doesn't reference Microsoft.EntityFrameworkCore.Design. This package is required for the Entity Framework Core Tools to work. Ensure your startup project is correct, install the package, and try again.
---

<div class="h9">Exception Message:</div>

```
PM> Add-Migration AddingEFExtensions
Build started...
Build succeeded.
Your startup project 'Z.MyStartupProjectName' doesn't reference Microsoft.EntityFrameworkCore.Design. This package is required for the Entity Framework Core Tools to work. Ensure your startup project is correct, install the package, and try again.
```

<div class="image-outer"><img src="https://www.learnentityframeworkcore.com/images/efcore/migrations/add-migration/troubleshooting-your-startup-project-doesn-t-reference-microsoft-entitiyframeworkcore-design.png" loading="lazy" alt="Exception - Startup project not reference Microsoft.EntityFrameworkCore.Design"></div>
 
<div class="h9">Cause:</div>

This error arises when the [Microsoft.EntityFrameworkCore.Design](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Design) package is not referenced in your `Startup Project`.

<div class="h9">Solution:</div>

Make sure you have installed or referenced the [Microsoft.EntityFrameworkCore.Design](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Design) package in your `Startup Project`. 

You can add this package via the NuGet Package Manager with:

```
Install-Package Microsoft.EntityFrameworkCore.Design
```

Or through the .NET CLI with:

```
dotnet add package Microsoft.EntityFrameworkCore.Design
```