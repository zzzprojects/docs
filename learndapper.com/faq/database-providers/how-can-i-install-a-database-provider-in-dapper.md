---
id: 3f04ee7c-67e1-4a57-9eb4-82441d50fc1d
position: 2
title: How do I install a database provider in Dapper?
---

To install a database provider in Dapper, you need to add the appropriate [NuGet](https://www.nuget.org/) package to your project. For example:

- **SQL Server**: You need to install the [Microsoft.Data.SqlClient](https://www.nuget.org/packages/Microsoft.Data.SqlClient) package
- **Oracle**: You need to install the [Oracle.ManagedDataAccess.Core](https://www.nuget.org/packages/Oracle.ManagedDataAccess.Core) package
- **MySQL**: You need to install the [MySql.Data](https://www.nuget.org/packages/MySql.Data) package
- **PostgreSQL**: You need install the [Npgsql](https://www.nuget.org/packages/Npgsql) package
- **SQLite**: You need to install the [System.Data.SQLite.Core](https://www.nuget.org/packages/System.Data.SQLite.Core) package

More than one package might exist for the same providers. We just have included the most popular one.