---
permaid: 1000228
Title: EF Core PostgreSQL - Learn how to install and use this provider
MetaDescription: Unlock the power of EF Core using the PostgreSQL Provider. Learn how to use a PostgreSQL database and which providers to install for entity framework core.
LastMod: 2023-02-22
tags: provider connection
---

# EF Core PostgreSQL: Discover how to install and use this provider

2 major NuGet packages support PostgreSQL:

- [Npgsql.EntityFrameworkCore.PostgreSQL](https://www.nuget.org/packages/Npgsql.EntityFrameworkCore.PostgreSQL/)
- [Devart.Data.PostgreSql.EFCore](https://www.nuget.org/packages/Devart.Data.PostgreSql.EFCore/)

## Npgsql.EntityFrameworkCore.PostgreSQL

`Npgsql.EntityFrameworkCore.PostgreSQL` is an Entity Framework Core provider built on top of Npgsql. It allows you to use the EF Core O/RM with PostreSQL. It mostly behaves like any other EFCore provider (e.g. SQL Server) 

### How to Use Npgsql.EntityFrameworkCore.PostgreSQL Provider

To use `Npgsql.EntityFrameworkCore.PostgreSQL` provider, the first step is to install [Npgsql.EntityFrameworkCore.PostgreSQL](https://www.nuget.org/packages/Npgsql.EntityFrameworkCore.PostgreSQL/) NuGet package. 

Now to use Entity Framework Core with PostgreSQL database, override the OnConfiguring method in the context class and set the PostgreSQL data provider using `UseNpgsql` method. 


```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
{
	optionsBuilder.UseNpgsql(@"host=localhost;database=postgres;user id=postgres;password=******;");
}
```

## Devart.Data.PostgreSql.EFCore

`Devart.Data.PostgreSql.EFCore` is an Entity Framework Core provider created by Devart.

### How to Use Devart.Data.PostgreSql.EFCore Provider

To use `Devart.Data.PostgreSql.EFCore` provider, the first step is to install the [Devart.Data.PostgreSql.EFCore](https://www.nuget.org/packages/Devart.Data.PostgreSql.EFCore/) NuGet package. 

Now to use Entity Framework Core with PostgreSQL database, override the OnConfiguring method in the context class and set the PostgreSQL data provider using `UsePostgreSql` method. 


```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
{
	optionsBuilder.UsePostgreSql(@"host=localhost;database=postgres;user id=postgres;password=******;");
}
```




