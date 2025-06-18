---
PermaID: 1000096
Title: Entity Framework PostgreSQL - Learn More About this Provider 
MetaDescription: Unlock the power of Entity Framework by learning how to use PostgreSQL providers in your project.
LastMod: 2025-06-18
Tags: provider postgresql
---

# Entity Framework PostgreSQL: Discover More About this Provider

## Introduction

`Npgsql.EntityFramework` is an Entity Framework provider built on top of Npgsql. It allows you to use the EF with PostgreSQL. It mostly behaves like any other EF provider (e.g., SQL Server) 

#### How to Use PostgreSQL Database Provider

To use the PostgreSQL database provider, the first step is to install [Npgsql.EntityFramework](https://www.nuget.org/packages/Npgsql.EntityFramework/) NuGet package. 

Now to use Entity Framework with the PostgreSQL database, add the connection string and the PostgreSQL provider for EF6 in the configuration sections of the `App.config` file

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