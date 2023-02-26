---
Permalink: articles/carloscds-ef6-connection-strings
Title: Managing Connection Strings in Entity Framework Code First
MetaDescription: Learn how to store connection strings and providers in Entity Framework Code First for C# with Visual Studio 2010. Explore three different ways to specify connection strings and providers.
LastMod: 2023-02-24
tags: carloscds article connection-string
OriginalLink: https://carloscds.net/2011/08/ef-codefirstgerenciando-as-strings-de-conexo/
CreatedDate: 2011-08-08
CreatedUserName: Carlos dos Santos
CreatedUserLink: https://carloscds.net/
---

# Managing Connection Strings in Entity Framework Code First

Today we're going to talk a little bit about managing connection strings in Entity Framework Code First, a very simple and quick way to create a relational object mapping in C # with Visual Studio 2010.

If you still don't know EF Code First, see my [post](/articles/carloscds-ef6-code-first).

What I will deal with in this post is how we can store the connection strings and providers that allow EF access to our database. By default, when creating an EF context, a connection string, and a Data Provider are needed, which indicates which database provider will be used, but if we create a simple context, with no connection string, as in the example below, what happens?

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Data.Entity;
 
namespace EFExemple
{
    public class ContextExample : DbContext
    {
        public DbSet<Client> Client { get; set; }
    }
}
```
 
As we do not specify any constructor, EF will try to use SQL Express server and will try to create the `ContextExample` database, and if you do not have this SQL server available, you will receive an error.

Let's imagine that you want to specify a connection string. In this case, we have several ways to do this and let's explore each one:

## Option 1 - Specify the connection string in the context constructor

```csharp
public class ContextExample : DbContext
{
    public DbSet<Client> Client { get; set; }
 
    public ContextExample(string connection) : base(connection)
    {
    }
}
```

In this case, we can use it in two ways: you can enter only the connection string, and in this case, you can use only SQL Server, which is the default provider for EF. That can be done in the context instance:

```csharp
static void Main(string[] args)
{
    ContextExample ctx = new ContextExample("data source=(local);initial catalog=EFExample;user id=test;password=test;");

    ctx.Database.CreateIfNotExists();
}
```

In this case, the `EFExample` database will be created on the local SQL server.

## Option 2: Specify the connection in the configuration file

The second way is to keep the string out of the executable, so that you can modify it at any time, in this case we will use the application's configuration file (`app.config` or `web.config`):

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
    <connectionStrings>
        <add name="EFExample" providerName="System.Data.SqlClient" connectionString="data source=(local);initial catalog=EFExample;user id=test;password=test;"/>
        <add name="EFExample-MySql" connectionString="Server=localhost;Database=efexample;Uid=test;Pwd=test;Port=3306;" providerName="MySql.Data.MySqlClient"/>
    </connectionStrings>
</configuration>
```

In the configuration file, we can now keep more than one string and also tell which database for each connection. To use the connection, simply specify it in the constructor:

```csharp
ContextExample ctx = new ContextExample("EFExample");
```

or

```csharp
ContextExample ctx = new ContextExample("EFExample-MySql");
```

## Option 3: Specify a DbConnection

`DBConnection` is the class that creates connections for EF Code First, in this case, you can generate the connection string more dynamically, and it does not need to be stored in an `app.config` or `web.config` file, which would leave the string more exposed. This mechanism is interesting when you want to encrypt the string, see:

```csharp
public class ContextExample : DbContext
{
    public DbSet<Client> Clients { get; set; }

    public ContextExample(string connection): base(connection)
    {
    }
 
    public ContextExample(DbConnection connection) : base(connection, true)
    {
    }
}
```

See that now we have an overload of the constructor, where we specify the `DbConnection`, and we will do this in the context instance:

```csharp
static void Main(string[] args)
{
    DbProviderFactory prov = DbProviderFactories.GetFactory("System.Data.SqlClient");
    DbConnection connection = prov.CreateConnection();
 
    connection.ConnectionString = "data source=(local);initial catalog=EFExample;user id=test;password=test;";
 
    ContextExample ctx = new ContextExample(connection);
 
    ctx.Database.CreateIfNotExists();
}
```

To create the connection, first, we need to identify the database provider through the `DbProviderFactory()`, and immediately after, we specify the connection string, which is then passed as a parameter to the context builder. Note that the connection string could come from an encrypted file or even from a web service, which would increase the security of the application.

Well, guys, I hope this post can be useful to you.