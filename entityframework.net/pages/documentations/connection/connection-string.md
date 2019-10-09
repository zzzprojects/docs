---
PermaID: 1000129
Name: Connection String
---

# Connection String

A connection string contains initialization information that is passed as a parameter from a data provider to a data source.

 - The syntax depends on the data provider, and the connection string is parsed during the attempt to open a connection. 
 - Connection strings used by the Entity Framework contain information used to connect to the underlying ADO.NET data provider that supports the Entity Framework. 

Typically an Entity Framework application uses a class derived from DbContext. 

 - This derived class will call one of the constructors on the base DbContext class to connect to a database, and that is, how a connection string is found/used.
 -  It creates a connection string for the database using either SQL Express or LocalDB. If both are installed, SQL Express will be used.

## By Convention

If you have not done any other configuration in your application, then calling the parameterless constructor on DbContext will cause DbContext to run in Code First mode with a database connection created by convention. 

```csharp
public class BookContext : DbContext
{
    public BookContext()
    {
    }

    public DbSet<Book> Books { get; set; }
}
```

In this example, DbContext uses the namespace qualified name of your derived context class .i.e `EFDemo.BookContext` as the database name.

## Specify Database Name

If you have not done any other configuration in your application, then calling the string constructor on DbContext with the database name you want to will cause DbContext to run in Code First mode with a database connection created by convention to the database of that name.

```csharp
public class BookContext : DbContext
{
    public BookContext() : base("BookDatabase")
    {
    }

    public DbSet<Book> Books { get; set; }
}
```

In this example, DbContext uses `BookDatabase` as the database name and creates a connection string for this database.

## Specify Connection String in Config File

You can also specify the connection string in your app.config or web.config file. It is an easy way to tell DbContext to use a database server other than SQL Express or LocalDB.

```csharp
<connectionStrings>
  <add name="BookDbContext" connectionString="Data Source=(localdb)\ProjectsV13;Initial Catalog=BookDatabase;" providerName="System.Data.SqlClient"/>
</connectionStrings>
```

 - If the name of the connection string matches the name of your context, then it will be found by DbContext when the parameterless constructor is used. 
 - If the connection string name is different from the name of your context, then you can pass the connection string name to the DbContext constructor.

```csharp
public class BookContext : DbContext
{
    public BookContext() : base("BookDbContext")
    {
    }

    public DbSet<Book> Books { get; set; }
}
```