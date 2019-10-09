---
PermaID: 1000096
Name: PostgreSQL Provider
---

# PostgreSQL

## Introduction

`Npgsql.EntityFramework` is an Entity Framework provider built on top of Npgsql. It allows you to use the EF with PostgreSQL. It mostly behaves like any other EF provider (e.g., SQL Server) 

#### How to Use PostgreSQL Database Provider

To use PostgreSQL database provider, the first step is to install [Npgsql.EntityFramework](https://www.nuget.org/packages/Npgsql.EntityFramework/) NuGet package. 

Now to use Entity Framework with PostgreSQL database, add connection string and PostgreSQL provider for EF6 in configuration sections of `App.config` file

```csharp
<connectionStrings>
  <add 
    name="Default" 
    connectionString="host=localhost;database=postgres;user id=postgres;password=**********;" 
    providerName="Npgsql" />
</connectionStrings>
<system.data>
  <DbProviderFactories>
    <remove invariant="Npgsql"/>
    <add 
      name="Npgsql Data Provider" 
      invariant="Npgsql" 
      description=".Net Data Provider for PostgreSQL" 
      type="Npgsql.NpgsqlFactory, Npgsql, Culture=neutral, PublicKeyToken=5d8b90d52f46fda7" 
      support="FF"/>
  </DbProviderFactories>
</system.data>
```