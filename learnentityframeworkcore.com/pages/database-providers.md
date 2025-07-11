---
title: Working With Providers in EF Core - EF Core Providers - C# EF Core Providers
description: EF Core is designed to work with a variety of providers, allowing users to access their data in different databases. The list of supported providers includes MySQL, PostgreSQL, Oracle Database, and Microsoft SQL Server. 
canonical: /database-providers
status: Published
lastmod: 2025-07-10
---

# EF Core Providers

EF Core is an open-source, lightweight, cross-platform, and extensible Object-Relational Mapping (ORM) framework for .NET. It provides a way to access and manipulate data in relational databases using .NET code. EF Core supports several database providers, including:

 - Microsoft SQL Server
 - Microsoft SQL Server Compact Edition
 - SQLite
 - MySQL
 - MariaDB
 - PostgreSQL
 - In-memory databases and more.

The choice of a database provider depends on the specific needs of the application and the target database system.

## EF Core SQL Server Provider

To use the SQL Server provider in EF Core, you need to follow these steps:

 1. Install the `Microsoft.EntityFrameworkCore.SqlServer` NuGet package in your project.
 2. Add a reference to the `Microsoft.EntityFrameworkCore.SqlServer` package in your code using a using statement.
 3. Define your model classes, which represent the entities in your application and the relationships between them.
 4. Create a context class that derives from `Microsoft.EntityFrameworkCore.DbContext` and represents a session with the database.
 5. In the context class, override the `OnConfiguring` method and use it to configure the SQL Server provider. You will need to specify the connection string to the SQL Server database.
 6. Use the context class to perform database operations, such as inserting, updating, deleting, and querying data.

Here's an example of how to use the SQL Server provider in EF Core:

```csharp
using Microsoft.EntityFrameworkCore;

namespace MyApp
{
    public class MyDbContext : DbContext
    {
        public DbSet<Blog> Blogs { get; set; }

        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        {
            optionsBuilder.UseSqlServer("Server=(localdb)\\mssqllocaldb;Database=MyDb;Trusted_Connection=True;");
        }
    }

    public class Blog
    {
        public int BlogId { get; set; }
        public string Url { get; set; }
    }
}
```

In this example, the `MyDbContext` class is the context class that represents a session with the database. The `Blog` class represents a blog entity in the database. The `OnConfiguring` method is used to configure the SQL Server provider and specify the connection string to the database. The `DbSet` property represents a table in the database and maps to the `Blog` entity.

## EF Core Azure Cosmos DB Provider

The `Microsoft.EntityFrameworkCore.Cosmos` is an EF Core database provider for Azure Cosmos DB. It allows you to interact with **Azure Cosmos DB** using Entity Framework Core. This provider supports LINQ queries, change tracking, and updates to data in Azure Cosmos DB, and it provides a way to write .NET applications that use Azure Cosmos DB as the back-end database.

To use `Microsoft.EntityFrameworkCore.Cosmos` in Entity Framework Core, you need to perform the following steps:

 1. Install the required NuGet package: `Microsoft.EntityFrameworkCore.Cosmos`.
 2. Define your model classes and configure them with the Entity Framework Core data annotations or the Fluent API.
 3. Create a context class that inherits from `Microsoft.EntityFrameworkCore.DbContext`.
 4. In the context class, override the `OnConfiguring` method to configure the Cosmos provider. You will need to provide the endpoint URL and the primary key for your Azure Cosmos DB account.
 5. In the context class, add a `DbSet` property for each entity in your model.
 6. Use the context class to interact with your data in Azure Cosmos DB.

Here's an example:

```csharp
using Microsoft.EntityFrameworkCore;

public class BlogContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
        optionsBuilder.UseCosmos("<endpoint-url>", "<primary-key>", "<database-name>");
    }
}

public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }
}
```

You can then use the context class to query and save data to Azure Cosmos DB.

## EF Core Oracle Provider

Oracle provider is a database provider for Oracle databases in EF Core. It enables you to use the LINQ to Entities API for querying the database and provides support for change tracking and updates. 

 - With the Oracle provider, you can use the full feature set of EF Core with Oracle databases, including LINQ, transactions, and migrations.
 - You can use the Oracle provider with EF Core which allows you to focus on the business logic instead of writing complex SQL statements.

To use the Oracle provider in EF Core, you need to install the required NuGet packages and configure your `DbContext` to use the Oracle provider. 

Here are the steps:

**Step 1:** Install the required NuGet packages:

```csharp
Install-Package Microsoft.EntityFrameworkCore
Install-Package Oracle.EntityFrameworkCore
```

**Step 2:** Now configure the `DbContext` to use the Oracle provider:

```csharp
using Microsoft.EntityFrameworkCore;
using Oracle.EntityFrameworkCore;

namespace MyNamespace
{
   public class MyDbContext : DbContext
   {
      protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
      {
         optionsBuilder.UseOracle(
            "User Id=yourUserId;Password=yourPassword;Data Source=yourDataSource;");
      }

      public DbSet<MyEntity> MyEntities { get; set; }
   }
}
```

Note that you need to replace `yourUserId`, `yourPassword`, and `yourDataSource` with your actual values.

**Step 3:** Use the `DbContext`: 

You can now use the `DbContext` to interact with the Oracle database. For example, you can insert, update, and query records using the `DbContext`:

```csharp
using (var context = new MyDbContext())
{
    var entity = new MyEntity { Name = "John Doe" };
    context.MyEntities.Add(entity);
    context.SaveChanges();

    var result = context.MyEntities.Where(e => e.Name == "John Doe").ToList();
    // result will contain the entity with the name "John Doe"
}
```

This is just a basic example to get you started with using `Oracle.EntityFrameworkCore` in EF Core. You can find more information and examples on how to use the provider in the Devart documentation.


## EF Core SQLite Provider

EF Core SQLite is a database provider for SQLite databases in EF Core. It provides support for LINQ to Entities, transactions, and migrations, making it easier to work with SQLite databases in .NET applications. 

 - EF Core SQLite is a lightweight and portable database option, and it can be used in a variety of scenarios, including desktop applications, mobile applications, and web applications. 
 - With the EF Core SQLite provider, you can use the full feature set of EF Core with SQLite databases, enabling you to write efficient and maintainable code.

To use the SQLite provider in EF Core, you need to follow these steps in detail:

**Step 1:** Install the `Microsoft.EntityFrameworkCore.Sqlite` NuGet package. 

You can install it using the following command in the Package Manager Console:

```csharp
Install-Package Microsoft.EntityFrameworkCore.Sqlite
```

**Step 2:** Add a new `DbContext` class to your project and inherit it from `Microsoft.EntityFrameworkCore.DbContext`. 

A `DbContext` class acts as a bridge between the database and your application. It contains information about the entities in the model and maps them to tables in the database.

```csharp
using Microsoft.EntityFrameworkCore;

public class MyDbContext : DbContext
{
    public DbSet<MyEntity> MyEntities { get; set; }
    ...
}
```

In the `DbContext` class, add a `DbSet` property for each entity in the model. A `DbSet` represents a collection of entities in the model that map to a table in the database.

**Step 3:** Use the `UseSqlite` method on the optionsBuilder instance in the `OnConfiguring` method to specify the SQLite database to be used. 

The `OnConfiguring` method is used to configure the options for the context. The `UseSqlite` method takes a connection string as a parameter that specifies the location and name of the SQLite database.

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
{
    optionsBuilder.UseSqlite("Data Source=mydb.db");
}
```

**Step 4:** Run migrations to create the database and tables.

EF Core can automatically create the database and tables based on the model by using migrations. To do this, you need to add a new migration and update the database using the following commands in the Package Manager Console:

```csharp
Add-Migration InitialCreate
Update-Database
```

These steps will create a new SQLite database file named **"mydb.db"** in the project's root directory, and generate the tables based on the entities in the model.

That's it! You're now ready to perform CRUD operations on the database using the `DbContext` instance.

## EF Core MySql Provider

EF Core can work with different database providers including MySQL, allowing developers to use the same code to target different databases. It provides an API for querying and manipulating data in the database.

Here's a basic example of how to use MySQL with EF Core:

**Step 1:** Install the required packages:

```csharp
Install-Package MySql.Data.EntityFrameworkCore
```

**Step 2:** Create a class that represents your database context:

```csharp
using Microsoft.EntityFrameworkCore;

namespace YourNamespace
{
    public class YourDbContext : DbContext
    {
        public DbSet<YourEntity> YourEntities { get; set; }

        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        {
            optionsBuilder.UseMySql("server=localhost;user=root;password=password;database=your_db");
        }
    }
}
```

**Step 3:** Create a class that represents your entity:

```csharp
namespace YourNamespace
{
    public class YourEntity
    {
        public int Id { get; set; }
        public string Name { get; set; }
    }
}
```

**Step 4:** Use the database context in your code to query and manipulate data:

```csharp
using (var context = new YourDbContext())
{
    context.YourEntities.Add(new YourEntity { Name = "Your Entity" });
    context.SaveChanges();

    var entities = context.YourEntities.ToList();
}
```

This is just a simple example to give you an idea of how to use MySQL with EF Core. There are many more advanced features and configurations available, such as configuring relationships between entities, executing raw SQL, and more.

## EF Core PostgreSQL Provider

EF Core allows you to interact with relational databases like PostgreSQL. It provides an API for querying and manipulating data in the database and allows developers to use the same code to target different databases.

Here's a basic example of how to use PostgreSQL with EF Core:

**Step 1:** Install the required `Npgsql.EntityFrameworkCore.PostgreSQL` package:

```csharp
Install-Package Npgsql.EntityFrameworkCore.PostgreSQL
```
**Step 2:** Create a class that represents your database context:

```csharp
using Microsoft.EntityFrameworkCore;

namespace YourNamespace
{
    public class YourDbContext : DbContext
    {
        public DbSet<YourEntity> YourEntities { get; set; }

        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        {
            optionsBuilder.UseNpgsql("Host=localhost;Database=your_db;Username=your_username;Password=your_password");
        }
    }
}
```

**Step 3:** Create a class that represents your entity:

```csharp
namespace YourNamespace
{
    public class YourEntity
    {
        public int Id { get; set; }
        public string Name { get; set; }
    }
}
```

**Step 4:** Use the database context in your code to query and manipulate data:

```csharp
using (var context = new YourDbContext())
{
    context.YourEntities.Add(new YourEntity { Name = "Your Entity" });
    context.SaveChanges();

    var entities = context.YourEntities.ToList();
}
```

This is just a simple example to give you an idea of how to use PostgreSQL with EF Core. There are many more advanced features and configurations available, such as configuring relationships between entities, executing raw SQL, and more.

## EF Core InMemory Provider

EF Core InMemory provider is an implementation of Entity Framework Core's database provider that allows developers to run their applications against an in-memory database. 

 - The in-memory database provides a fast and efficient way to test applications without the need for a separate test database. 
 - EF Core InMemory provider can be used in both unit tests and integration tests, making it an ideal tool for testing database-driven applications.

To use the EF Core InMemory provider in your .NET application, you need to perform the following steps:

**Step 1:** Install the NuGet Package: 

First, you need to install the `Microsoft.EntityFrameworkCore.InMemory` NuGet package. You can do this by running the following command in the Package Manager Console:

```csharp
Install-Package Microsoft.EntityFrameworkCore.InMemory
```

**Step 2:** Define your `DbContext`: 

Next, you need to define your `DbContext` class and specify the InMemory provider in the `OnConfiguring` method:

```csharp
public class MyDbContext : DbContext
{
    public DbSet<MyEntity> MyEntities { get; set; }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
        optionsBuilder.UseInMemoryDatabase("MyInMemoryDb");
    }
}
```

**Step 3:** Use the `DbContext`: 

In your application code, you can use the `DbContext` to interact with the in-memory database. You can add, update, and query data using the `DbContext`, just as you would with a regular database:

```csharp
using (var context = new MyDbContext())
{
    var entity = new MyEntity { Name = "John Doe" };
    context.MyEntities.Add(entity);
    context.SaveChanges();

    var result = context.MyEntities.Where(e => e.Name == "John Doe").ToList();
    // result will contain the entity with the name "John Doe"
}
```

Note that the in-memory database will be recreated each time the application is run, so it is important to use an appropriate persistence mechanism such as a file-based or SQL database for production scenarios.

## EF Core Pomelo MySql Provider

EF Core `Pomelo.EntityFrameworkCore.MySql` is an Entity Framework Core provider for the MySQL database engine. It allows .NET developers to interact with a MySQL database using EF Core. This provider is an open-source implementation and is maintained by the Pomelo Foundation.

To use `Pomelo.EntityFrameworkCore.MySql` in EF Core, follow these steps:

**Step 1:** Install the NuGet Package: 

To use Pomelo.EntityFrameworkCore.MySql, you need to install the Pomelo.EntityFrameworkCore.MySql NuGet package. You can install it by using the Package Manager Console in Visual Studio and running the following command:

```csharp
Install-Package Pomelo.EntityFrameworkCore.MySql
```

**Step 2:** Define your `DbContext`: 

Next, you need to define your `DbContext` class and specify the Pomelo.EntityFrameworkCore.MySql provider in the OnConfiguring method:

```csharp
public class MyDbContext : DbContext
{
    public DbSet<MyEntity> MyEntities { get; set; }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
        optionsBuilder.UseMySql("Server=localhost;Database=testdb;User=root;Password=password;");
    }
}
```

In the example, the connection string is passed to the `UseMySql` method to configure the `DbContext` to use the `Pomelo.EntityFrameworkCore.MySql` provider.

**Step 3:** Use the `DbContext`: 

You can now use the `DbContext` to interact with the MySQL database. For example, you can insert, update, and query records using the `DbContext`:

```csharp
using (var context = new MyDbContext())
{
    var entity = new MyEntity { Name = "John Doe" };
    context.MyEntities.Add(entity);
    context.SaveChanges();

    var result = context.MyEntities.Where(e => e.Name == "John Doe").ToList();
    // result will contain the entity with the name "John Doe"
}
```

This is a basic example to get you started with using `Pomelo.EntityFrameworkCore.MySql` in EF Core. You can find more information and examples on how to use the provider in the `Pomelo.EntityFrameworkCore.MySql` documentation.

## EF Core Devart MySql Provider

The `Devart.Data.MySql.EFCore` is a provider of the MySQL database engine for Entity Framework Core (EF Core). It allows .NET developers to interact with a MySQL database using EF Core. This provider is a commercial product developed and maintained by Devart.

To use `Devart.Data.MySql.EFCore` in EF Core, you need to follow these steps:

**Step 1:** Install the NuGet Package: 

Install the `Devart.Data.MySql.EFCore` NuGet package in your .NET application using the Package Manager Console in Visual Studio. The command to install the package is:

```csharp
Install-Package Devart.Data.MySql.EFCore
```

**Step 2:** Configure the `DbContext`: 

In your application, define a `DbContext` class and configure it to use the `Devart.Data.MySql.EFCore` provider in the `OnConfiguring` method. For example:

```csharp
public class MyDbContext : DbContext
{
    public DbSet<MyEntity> MyEntities { get; set; }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
        optionsBuilder.UseMySql("Server=localhost;Database=testdb;User=root;Password=password;");
    }
}
```

In the example, the connection string is passed to the `UseMySql` method to configure the `DbContext` to use the `Devart.Data.MySql.EFCore` provider.

**Step 3:** Use the `DbContext`: 

You can now use the `DbContext` to interact with the MySQL database. For example, you can insert, update, and query records using the `DbContext`:

```csharp
using (var context = new MyDbContext())
{
    var entity = new MyEntity { Name = "John Doe" };
    context.MyEntities.Add(entity);
    context.SaveChanges();

    var result = context.MyEntities.Where(e => e.Name == "John Doe").ToList();
    // result will contain the entity with the name "John Doe"
}
```

This is just a basic example to get you started with using `Devart.Data.MySql.EFCore` in EF Core. You can find more information and examples on how to use the provider in the Devart documentation.

## EF Core Devart Oracle Provider

The `Devart.Data.Oracle.EFCore` is a third-party provider for Entity Framework Core (EF Core) that enables EF Core to interact with an Oracle database. 

 - It provides a set of APIs for performing database operations such as inserting, updating, and retrieving data from an Oracle database.
 - By using `Devart.Data.Oracle.EFCore`, you can similarly interact with an Oracle database as you would interact with a relational database using EF Core. 
 - This provider allows you to use the EF Core API to perform database operations and maps your .NET objects to tables in an Oracle database.

To use `Devart.Data.Oracle.EFCore` in EF Core, you need to follow these steps:

**Step 1:** Install the NuGet Package: 

Install the Devart.Data.Oracle.EFCore NuGet package in your .NET application using the Package Manager Console in Visual Studio. The command to install the package is:

```csharp
Install-Package Devart.Data.Oracle.EFCore
```

**Step 2:** Configure the `DbContext`: 

In your application, define a `DbContext` class and configure it to use the Devart.Data.Oracle.EFCore provider in the OnConfiguring method. For example:

```csharp
public class MyDbContext : DbContext
{
    public DbSet<MyEntity> MyEntities { get; set; }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
        optionsBuilder.UseOracle("Data Source=OracleDB;User Id=UserName;Password=Password;");
    }
}
```

In the example, the connection string is passed to the UseOracle method to configure the DbContext to use the Devart.Data.Oracle.EFCore provider.

**Step 3:** Use the `DbContext`: 

You can now use the `DbContext` to interact with the Oracle database. For example, you can insert, update, and query records using the DbContext:

```csharp
using (var context = new MyDbContext())
{
    var entity = new MyEntity { Name = "John Doe" };
    context.MyEntities.Add(entity);
    context.SaveChanges();

    var result = context.MyEntities.Where(e => e.Name == "John Doe").ToList();
    // result will contain the entity with the name "John Doe"
}
```

This is just a basic example to get you started with using `Devart.Data.Oracle.EFCore` in EF Core. You can find more information and examples on how to use the provider in the Devart documentation.
