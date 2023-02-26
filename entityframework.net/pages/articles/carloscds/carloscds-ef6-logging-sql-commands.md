---
Permalink: articles/carloscds-ef6-logging-sql-commands
Title: Migrating to Entity Framework 6: New Features to Explore
MetaDescription: Learn about the new features of Entity Framework 6 and how to migrate from version 5. In this article, we explore the command log feature to see what happens under the hood.
LastMod: 2023-02-24
tags: carloscds article logging
OriginalLink: https://carloscds.net/2013/09/entity-framework-6gerando-log-dos-comandos-sql/
CreatedDate: 2013-09-02
CreatedUserName: Carlos dos Santos
CreatedUserLink: https://carloscds.net/
---

# Migrating to Entity Framework 6: New Features to Explore

Entity Framework 6 RC has already been released! Now is the time to start working on migrating from version 5 to 6 and see the new features. Specifications on the new features can be found [here](https://docs.microsoft.com/en-us/ef/ef6/what-is-new/past-releases).

Among the many new features, which we will explore in the following posts, today I will highlight one that I find very interesting: the command log sent to the database.

An ORM (Object-Relational Mapping), translates commands from a programming language, in our case C # and LINQ, to database commands. ORM efficiency is directly related to how the commands for the database are generated, to take the commands written in C #, and convert them into SQL commands.

In version 6 of the Entity Framework, a new object was introduced that allows us to intercept the commands that are being sent to the database and so we can see what happens "under the hood". That can also be done by placing a database profiler tool. In the case of Microsoft SQL Server, this tool is SQL Server Profiler, but there are several others.

In the case of our example, we will execute a method for each LINQ operation that will be executed in the Entity Framework. To start our example, we will create a Console Application project using .NET 4.5 (the use of .NET 4.5 is important to support all the features of Entity Framework 6).

Creating a new Console Application project:

<img src="https://www.carloscds.net/wp-content/uploads/2013/09/SNAGHTML476eb602_thumb2.png" width="532" height="369" alt="Create console application project">

After creating our project, we will add the Entity Framework through the [Nuget](https://nuget-tutorial.net/) window, which is in Tools -> Library Packager Manager -> `Packager Manager Console` and inside it, we will execute the command below:

```package-manager
PM> Install-Package EntityFramework –Pre
```

Note that the “-Pre” option will install Entity Framework 6 RC, as it has not yet been officially released and is still a release candidate.

Now let's create a straightforward context with a `Client` class. For this, add a new class and call it `Context`, as in the example below:

```csharp
public class Context : DbContext
{
    public DbSet<Client> Clients {get; set;}
}
```

Now let's add the `Client` class:

```csharp
public class Client
{
    public int ID { get; set; }
    public string Name { get; set; }
}
```

Finally, we will modify the `App.Config` file to direct the location and name of the database to be created: (this is just an example, and your file may look different)

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
    <connectionStrings>
        <add name="Context" connectionString="data source=(local);initial catalog=EFLog;integrated security=true;" providerName="System.Data.SqlClient"/>
    </connectionStrings>
</configuration>
```

Now let's get to the point. Let's add the SQL LOG. For this, in `Program.cs`, we will insert the following commands:

```csharp
class Program
{
    static void Main(string[] args)
    {
        var db = new Context();
        db.Database.Log = CommandLog;

        var cli = new Client() { Name = "Carlos" };
        db.Clients.Add(cli);
        db.SaveChanges();

        var clients =   from c in db.Cliente
                        select c;
  
        foreach(var c in clients)
        {
            Console.WriteLine(c.Name);
        }
    }

    public static void CommandLog(string sql)
    {
        Console.WriteLine("Command SQL: "+sql);
    }
}
```

The `CommandLog()` method is assigned to the `Log` property of the `Context.Database`. Therefore, all commands sent to the database are also sent to this method. Inside it, we simply call the `Console.WriteLine()` method to show the commands on the screen.

When running the program, you will see something like the image below:

<img src="https://www.carloscds.net/wp-content/uploads/2013/09/SNAGHTML47817c48_thumb2.png" width="676" height="443" alt="CommandLog">

That is a very simple implementation of the Command Log implementation available now in version 6, but you can certainly use it in a much more elaborate way in your projects.