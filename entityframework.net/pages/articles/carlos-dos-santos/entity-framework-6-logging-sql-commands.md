---
Permalink: articles/entity-framework-6-logging-sql-commands
name: EF6 - Logging SQL Commands
tags: article logging carlos-dos-santos 
OriginalLink: http://carloscds.net/2013/09/entity-framework-6gerando-log-dos-comandos-sql/
CreatedDate: 2020-09-02
CreatedUserName: Carlos dos Santos
CreatedUserLink: http://carloscds.net/
---

# Entity Framework 6 - Logging SQL Commands

Among the many new features of Entity Framework 6, the highlight of this article will be on the command log sent to the database.

An ORM (Object-Relational Mapping), translates commands from a programming language, in our case C# and LINQ, to database commands. The ORM efficiency is related to how the commands for the database are generated; to take the commands written in C# and convert them into SQL commands.

In Entity Framework 6, a new object was introduced that allows us to intercept the commands sent to the database, and to see what happens "under the hood". This can also be done by placing a database profiler tool. In the case of Microsoft SQL Server, this tool is SQL Server Profiler, but there are several others.

In this example, we will execute a method for each operation in LINQ that will be performed in the Entity Framework.

The first step will be to create a Console Application project using .Net 4.5 (the use of .Net 4.5 is important to support all Entity Framework 6 functionality).

Once the project created, we will add the Entity Framework through the Nuget window. This step is in Tools / Library Packager Manager / Packager Manager Console, and inside it, we will execute the command below:

```csharp
PM> Install-Package EntityFramework
```

Let's create a context with a `Client` class. For this, add a new class and call it `Context`, as in the example below:

```csharp
public class Context: DbContext
{
    public DbSet<Client> Clients {get; set;} 
}
```

Now let's add the `Client` class:

```csharp
public class Client
{
  public int ID {get; set; }
  public string Name {get; set; }
}
```

Finally, we will modify the App.Config file to direct the location and name of the database to be created.

```csharp
<?xml version="1.0" encoding="utf-8"?>
<configuration>
	<connectionStrings>
		<add name="Contexto" connectionString="data source=(local); initial catalog=EFLog; integrated security=true;" providerName="System.Data.SqlClient"/>
	</connectionStrings>
</configuration>
```

Let now add the SQL Log. For this, in Program.cs, we will insert the following commands:

``` csharp
class Program
{
    static void Main ( string [] args)
    {
        var db = new Context();
        db.Database.Log = RecordLog;

        var cli = new Client() { Name = "Carlos" };
        db.Clients.Add(cli);
        db.SaveChanges();

        query clients = from c in db.Clients
						select c;
                     
        foreach(var c in clients)
        {
            Console.WriteLine(c.Name);
        }
    }

    public static void RecordLog(string sql)
    {
        Console.WriteLine("SQL Command:" + sql);
    }
}
```

The `RecordLog` method is assigned to the Context Database of the Log property. Therefore, all commands sent to the database are calling Console.WriteLine() method.

Finally, the Command Log implementation is available in version 6, but it can be used in more elaborate ways.