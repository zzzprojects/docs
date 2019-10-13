---
PermaID: 1000095
Name: Database Provider Supported
---

# Supported Database Providers

## What database provider are supported in Entity Framework?  

Data Provider is a set of libraries that are used to connect to a database, executing commands, and retrieving results. For example, SQL data provider for SQL, Oracle data provider for Oracle, OLE DB data provider for access, excel or mysql, etc. 

Entity Framework uses a provider model to allow EF to be used to access many different databases.

### Microsoft.EntityFrameworkCore.SqlServer

It allows Entity Framework Core to be used with Microsoft SQL Server (including SQL Azure). The provider is maintained as part of the EntityFramework GitHub project.

#### Install

`PM> Install-Package Microsoft.EntityFrameworkCore.SqlServer`

NuGet Url: [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/)

### System.Data.SQLite

The official SQLite database engine for both x86 and x64 along with the ADO.NET provider.  This package includes support for LINQ and Entity Framework 6.

#### Install

`PM> Install-Package System.Data.SQLite`

NuGet Url: [https://www.nuget.org/packages/System.Data.SQLite/](https://www.nuget.org/packages/System.Data.SQLite/)

### Microsoft.EntityFrameworkCore.Sqlite 

This database provider allows Entity Framework Core to be used with SQLite. The provider is maintained as part of the EntityFramework GitHub project.

#### Install

`PM> Install-Package Microsoft.EntityFrameworkCore.Sqlite`

The SQLite provider has some migrations [limitations](https://docs.microsoft.com/en-us/ef/core/providers/sqlite/limitations). Most of these limitations are a result of limitations in the underlying SQLite database engine and are not specific to EF.

NuGet Url: [Microsoft.EntityFrameworkCore.Sqlite](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite/)

### Npgsql.EntityFrameworkCore.PostgreSQL

It allows Entity Framework Core to be used with PostgreSQL. The provider is maintained as part of the Npgsql.

#### Install

`PM> Install-Package Npgsql.EntityFrameworkCore.PostgreSQL`

NuGet Url: [Npgsql.EntityFrameworkCore.PostgreSQL](https://www.nuget.org/packages/Npgsql.EntityFrameworkCore.PostgreSQL/)

### IBM.EntityFrameworkCore

It allows Entity Framework Core to be used with IBM Data Server (DB2). The provider is maintained by IBM.

#### Install

`PM> Install-Package IBM.EntityFrameworkCore`

NuGet Url: [IBM.EntityFrameworkCore](https://www.nuget.org/packages/IBM.EntityFrameworkCore/)

### MySql.Data.EntityFrameworkCore

It allows Entity Framework Core to be used with MySQL. The provider is maintained as part of the [MySQL project](https://dev.mysql.com/).

#### Install

`PM> Install-Package MySql.Data.EntityFrameworkCore -Pre`

NuGet Url: [MySql.Data.EntityFrameworkCore](https://www.nuget.org/packages/MySql.Data.EntityFrameworkCore/8.0.10-rc)

### Pomelo.EntityFrameworkCore.MySql

It allows Entity Framework Core to be used with MySQL. The provider is maintained as part of the [Pomelo Foundation Project](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MySql).

#### Install

`PM> Install-Package Pomelo.EntityFrameworkCore.MySql`

NuGet Url: [Pomelo.EntityFrameworkCore.MySql](https://www.nuget.org/packages/Pomelo.EntityFrameworkCore.MySql/)

### Connector/Net

Connector/Net is a fully-managed ADO.NET driver for MySQL.

#### Install

See the [MySQL Connector documentation](https://dev.mysql.com/doc/connector-net/en/connector-net-installation.html) for installation instructions.

### EntityFrameworkCore.SqlServerCompact

It allows Entity Framework Core to be used with SQL Server Compact Edition. The provider is maintained as part of the [ErikEJ/EntityFramework.SqlServerCompact GitHub Project](https://github.com/ErikEJ/EntityFramework.SqlServerCompact).

#### Install

To work with SQL Server Compact Edition 4.0, install the [EntityFrameworkCore.SqlServerCompact40](https://www.nuget.org/packages/EntityFrameworkCore.SqlServerCompact40).

`PM> Install-Package EntityFrameworkCore.SqlServerCompact40`

To work with SQL Server Compact Edition 3.5, install the [EntityFrameworkCore.SqlServerCompact35](https://www.nuget.org/packages/EntityFrameworkCore.SqlServerCompact35).

`PM> Install-Package EntityFrameworkCore.SqlServerCompact35`

### Oracle Data Provider for .NET

Use Entity Framework with Oracle Data Provider for .NET.

#### Install

See the [Oracle Data Provider for .NET documentation](http://www.oracle.com/technetwork/topics/dotnet/whatsnew/index.html) for installation instructions.

### In-Memory (for Testing)

It allows Entity Framework Core to be used with an in-memory database. This is useful when testing code that uses Entity Framework Core. The provider is maintained as part of the [EntityFramework GitHub project](https://github.com/aspnet/EntityFramework).

#### Install

`PM> Install-Package Microsoft.EntityFrameworkCore.InMemory`

NuGet Url: [Microsoft.EntityFrameworkCore.InMemory](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory/)

### Devart

Devart is a third party provider writer that offers database providers for a wide range of databases (MySQL, Oracle, PostgreSQL, SQLite, DB2, and more).  Devart dotConnect is a commercial third party provider. Entity Framework support is only available in paid versions of dotConnect.

#### Install

See the [Devart dotConnect documentation](https://www.devart.com/dotconnect/) for installation instructions.

### DataDirect

Progress DataDirect offers unique data connectivity solutions for enterprises needing to better integrate data across Relational, Big Data and Cloud Databases. 

#### Install

See the [DataDirect documentation](https://www.progress.com/datadirect-connectors) for installation instructions.

### MyCat

It allows Entity Framework Core to be used with MyCat. The provider is maintained as part of the [Pomelo](https://github.com/PomeloFoundation/Entity-Framework-Core-MyCat-Proxy)

#### Install

Download and run the latest release from the [project site](https://github.com/PomeloFoundation/Entity-Framework-Core-MyCat-Proxy/releases).

### EntityFrameworkCore.FirebirdSQL

It allows Entity Framework Core to be used with FirebirdSQL. The provider is maintained as part of the [ralmsdeveloper/EntityFrameworkCore.FirebirdSQL GitHub Project](https://github.com/ralmsdeveloper/EntityFrameworkCore.FirebirdSQL).

#### Install

`PM> Install-Package EntityFrameworkCore.FirebirdSQL`

NuGet Url: [Microsoft.EntityFrameworkCore.InMemory](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory/)

### SSAS

SSAS Entity Framework Provider enables you to use LINQ and Microsoft ADO.NET Entity Framework to query OLAP cubes, offline cube files and SSAS in-memory Tabular model (BISM) like a regular relational database.

#### Install

See the [SSAS Entity Framework Provider documentation](http://agiledesignllc.com/Products.htm) for installation instructions.