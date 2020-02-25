---
permaid: 1000228
name: PostgreSQL
tags: provider connection
---

# PostgreSQL

## Introduction

`Npgsql.EntityFrameworkCore.PostgreSQL` is an Entity Framework Core provider built on top of Npgsql. It allows you to use the EF Core O/RM with PostreSQL. It mostly behaves like any other EFCore provider (e.g. SQL Server) 

#### How to Use PostgreSQL Database Provider

To use PostgreSQL database provider, the first step is to install [Npgsql.EntityFrameworkCore.PostgreSQL](https://www.nuget.org/packages/Npgsql.EntityFrameworkCore.PostgreSQL/2.1.0-preview2) NuGet package. 

Now to use Entity Framework Core with PostgreSQL database, override the OnConfiguring method in the context class and set the PostgreSQL data provider using `UseNpgsql` method. 


```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
{
	optionsBuilder.UseNpgsql(@"host=localhost;database=postgres;user id=postgres;password=******;");
}
```


