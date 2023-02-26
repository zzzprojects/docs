---
permaid: 1000225
Title: EF Core Providers - Learn how to install and use those providers
MetaDescription: Unlock the power of EF Core using available database providers. Learn about Pomelo, SQL Compact, Firebird and more.
LastMod: 2023-02-22
tags: provider connection
---

# EF Core Providers: Discover how to install and use those providers

A variety of sources builds EF Core providers and all of them are not maintained as part of the Entity Framework Core Project. Below are some more providers available for EF Core

You can find the full provider list supported here: <a href="https://docs.microsoft.com/en-us/ef/core/providers/" target="_blank">https://docs.microsoft.com/en-us/ef/core/providers/</a>

### Pomelo.EntityFrameworkCore.MySql

It allows Entity Framework Core to be used with MySQL. The provider is maintained as part of the [Pomelo Foundation Project](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MySql).

#### Install

`PM> Install-Package Pomelo.EntityFrameworkCore.MySql`

NuGet Url: [Pomelo.EntityFrameworkCore.MySql](https://www.nuget.org/packages/Pomelo.EntityFrameworkCore.MySql/)

### Pomelo.EntityFrameworkCore.MyCat

Pomelo.EntityFrameworkCore.MyCat is an Entity Framework Core provider and optimized for MyCat (An open source MySQL cluster proxy which based on Cobar)

#### Install

`PM> Install-Package Pomelo.EntityFrameworkCore.MyCat`

NuGet Url: [Pomelo.EntityFrameworkCore.MyCat](https://www.nuget.org/packages/Pomelo.EntityFrameworkCore.MyCat)

### EntityFrameworkCore.SqlServerCompact40

Allows SQL Server Compact 4.0 to be used with Entity Framework Core

#### Install

`PM> Install-Package EntityFrameworkCore.SqlServerCompact40`

NuGet Url: [EntityFrameworkCore.SqlServerCompact40](https://www.nuget.org/packages/EntityFrameworkCore.SqlServerCompact40)

### EntityFrameworkCore.SqlServerCompact35

Allows SQL Server Compact 3.5 SP2 to be used with Entity Framework Core. Requires the SQL Server Compact 3.5 SP2 runtime to be installed.

#### Install

`PM> Install-Package EntityFrameworkCore.SqlServerCompact35`

NuGet Url: [EntityFrameworkCore.SqlServerCompact35](https://www.nuget.org/packages/EntityFrameworkCore.SqlServerCompact35)

### FirebirdSql.EntityFrameworkCore.Firebird

The Entity Framework Core Provider for Firebird enables you to develop .NET applications that connect to the Firebird database using Entity Framework Core.

#### Install

`PM> Install-Package FirebirdSql.EntityFrameworkCore.Firebird`

NuGet Url: [FirebirdSql.EntityFrameworkCore.Firebird](https://www.nuget.org/packages/FirebirdSql.EntityFrameworkCore.Firebird)

### EntityFrameworkCore.FirebirdSQL

EntityFrameworkCore.FirebirdSql is an ORM, created at the top of the Firebird ADO.NET (FirebirdSql.Data.FirebirdClient) Data Provider. It allows you to use Entity Framework Core 2.0 as an extension, to access Firebird (2.x, 3.x, 4.x)

#### Install

`PM> Install-Package EntityFrameworkCore.FirebirdSQL`

NuGet Url: [EntityFrameworkCore.FirebirdSQL](https://www.nuget.org/packages/EntityFrameworkCore.FirebirdSQL)

### IBM.EntityFrameworkCore

It allows Entity Framework Core to be used with IBM Data Server (DB2) and IBM maintains it.

#### Install

`PM> Install-Package IBM.EntityFrameworkCore`

NuGet Url: [IBM.EntityFrameworkCore](https://www.nuget.org/packages/IBM.EntityFrameworkCore/)

### Devart.Data.PostgreSql.EFCore

It is a third party database provider and allows Entity Framework Core to be used with PostgreSql database.

#### Install

`PM> Install-Package Devart.Data.PostgreSql.EFCore`

NuGet Url: [Devart.Data.PostgreSql.EFCore](https://www.nuget.org/packages/Devart.Data.PostgreSql.EFCore)

### Devart.Data.SQLite.EFCore

It is a third party database provider and allows Entity Framework Core to be used with SQLite database.

#### Install

`PM> Install-Package Devart.Data.SQLite.EFCore`

NuGet Url: [Devart.Data.SQLite.EFCore](https://www.nuget.org/packages/Devart.Data.SQLite.EFCore)

### Devart.Data.MySql.EFCore

It is a third party database provider and allows Entity Framework Core to be used with MySql database.

#### Install

`PM> Install-Package Devart.Data.MySql.EFCore`

NuGet Url: [Devart.Data.MySql.EFCore](https://www.nuget.org/packages/Devart.Data.MySql.EFCore)

### EntityFrameworkCore.Jet

It allows Entity Framework Core to be used with Jet (Microsoft Access mdb or accdb format files)

#### Install

`PM> Install-Package EntityFrameworkCore.Jet -Version 2.0.0`

NuGet Url: [EntityFrameworkCore.Jet](https://www.nuget.org/packages/EntityFrameworkCore.Jet/)

