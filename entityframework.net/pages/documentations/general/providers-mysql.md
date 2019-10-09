---
PermaID: 1000097
Name: MySQL Provider
---

# MySQL

## Introduction

You can also use `MySQL Connector/Net` which integrates support for Entity Framework. [MySql.Data.Entity](https://www.nuget.org/packages/MySql.Data.Entity/7.0.7-m61) allows Entity Framework Core to be used with MySQL. The provider is maintained as part of the [MySQL](https://dev.mysql.com/) project. 

## Requirements

 - [Install Connector/Net](https://dev.mysql.com/doc/connector-net/en/connector-net-installation-windows.html)
 - [MySQL Server 5.5 or higher](https://dev.mysql.com/downloads/)

## How to Use MySQL Database Provider

To use MySQL database provider, the first step is to install [MySql.Data.Entity](https://www.nuget.org/packages/MySql.Data.Entity/7.0.7-m61) NuGet package.

Now to use Entity Framework with MySQL database, configure Connector/NET support for EF6 by adding connection string and the Connector/NET provider for EF6 in configuration sections of `App.config` file. 

```csharp
<connectionStrings>
    <add name="MyContext" providerName="MySql.Data.MySqlClient"
        connectionString="server=localhost;port=3306;database=mycontext;uid=root;password=********"/>
</connectionStrings>
<entityFramework>
    <defaultConnectionFactory type="System.Data.Entity.Infrastructure.SqlConnectionFactory, EntityFramework"/>
    <providers>
        <provider invariantName="MySql.Data.MySqlClient"
            type="MySql.Data.MySqlClient.MySqlProviderServices, MySql.Data.Entity.EF6"/>
        <provider invariantName="System.Data.SqlClient"
            type="System.Data.Entity.SqlServer.SqlProviderServices, EntityFramework.SqlServer"/>
    </providers>
</entityFramework>
```

Unless Connector/NET was installed with the standalone MSI or MySQL Installer, which adds the reference, insert the following data provider information into the config file.

```csharp
<system.data>
   <DbProviderFactories>
     <remove invariant="MySql.Data.MySqlClient" />
     <add name="MySQL Data Provider" invariant="MySql.Data.MySqlClient" description=".Net Framework Data Provider for MySQL" 
          type="MySql.Data.MySqlClient.MySqlClientFactory, MySql.Data, Version=8.0.10.0, Culture=neutral, PublicKeyToken=c5687fc88969c44d" />
   </DbProviderFactories>
</system.data>
```