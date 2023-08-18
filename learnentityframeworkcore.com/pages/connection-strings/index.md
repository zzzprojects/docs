---
title: Connection String in Entity Framework Core
description: Connection string contain information about the data source that is being connected to. This information varies from provider to provider, but will usually include the name and location of the source, and optionally some means of authenticating the user
canonical: connection-strings
status: Published
lastmod: 2023-02-24
---


# EF Core Connection String

Connection string contain information about the data source that is being connected. This information varies from provider to provider, but will usually include the name and location of the source, and optionally some means of authenticating the user. The information is provided as key/value pairs, separated by semi-colons. The following example shows a connection string for a SQL Server database named _Test_, attached to the default, an unnamed instance of SQL Server:

```
server=.;database=Test;trusted_user=true;
```

Connection strings for most providers are [documented here](https://www.connectionstrings.com/).  

## Configuring The Connection String

Once you have constructed your connection string, you need to make it available to your `DbContext`. There are several ways to achieve this.

### Overriding The OnConfiguring Method

The `DbContext` type has a virtual `OnConfiguring` method which is designed to be overridden so that you can provide configuration information for the context via the method's `DbContextOptionsBuilder` parameter. The `OnConfiguring` method is called every time an instance of the context is created. 

Most providers include convenience methods that extend the `DbContextOptionsBuilder` type, so that configuration can be simplified. The `UseSqlServer` method configures the context to use a SQL Server database, and takes a string representing the connection string as a parameter:

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
{
    optionsBuilder.UseSqlServer("server=.;database=myDb;trusted_connection=true;");
}
```
If you choose to set the connection string in the `OnConfiguring` method, this will override any other configuration.

### Configuring As A Service

In .NET Core applications, a configuration is more likely to be placed in the `Startup` class via the ServiceCollection's AddDbContext extension method:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<MyDbContext>(options => {
        options.UseSqlServer("server=.;database=myDb;trusted_connection=true;"));
    });
}
```

## Storing Connection Strings

The examples so far show connection strings being passed directly to the `DbContext`. You may prefer to keep your connection strings in configuration files so that you can change them without needing to recompile the application or use the features that allow different configuration settings to be used based on the environment.

The following snippet shows how a connection string can be added to an appsettings.json file:

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Warning"
    }
  },
  "AllowedHosts": "*",
  "ConnectionStrings": {
    "MyConnection": "server=.;database=myDb;trusted_connection=true;"
  }
}
```

Now you can use the Configuration API to pass the connection string to the `DbContext` in the Startup class:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<ConfigurationContext>(options => {
        options.UseSqlServer(Configuration.GetConnectionString("MyConnection"));
    });
}
```

