---
Permalink: articles/carloscds-ef6-interceptor
Title: Implementing Entity Framework Command Interception in Code
MetaDescription: Learn how to intercept commands sent to the database from Entity Framework using the DbConfiguration class and generate a log text file. This tutorial shows you how to configure your model using code instead of configuration files.
LastMod: 2023-02-24
tags: carloscds article interceptor logger
OriginalLink: https://carloscds.net/2013/12/entity-frameworkcodebase-configuration-interception/
CreatedDate: 2013-12-30
CreatedUserName: Carlos dos Santos
CreatedUserLink: https://carloscds.net/
---

# Implementing Entity Framework Command Interception in Code

One of the new features of Entity Framework 6 is the possibility of configuring our model using code instead of the traditional configuration files (`app.config` / `web.config`). Also, we now have many other configurations that can be modified using the DbConfiguration class.

Among the various configuration options we have through `DbConfiguration`, there is an Interception feature (see more [here](https://docs.microsoft.com/en-us/ef/ef6/fundamentals/logging-and-interception)), where we can intercept several operations that are sent to the database from Entity Framework. In this example, we will intercept the sending of commands to the database and generate a log text file.

## Creating the sample project:

To start, let's create a Console project using Visual Studio 2013, and then we'll add the Entity Framework through [NuGet](https://www.nuget.org/):

<img src="https://www.carloscds.net/wp-content/uploads/2013/12/image_thumb2.png" width="787" height="523" alt="create a Console project using Visual Studio 2013">

## Adding the Entity Framework Code First:

After creating the project, we will add the Entity Framework through NuGet. To do this, open the Tools / Library Package Manager / Package Manager Console menu and then type the command below:

```package-manager
PM> Install-Package EntityFramework
```

In this example, I am also using the [Entity Framework Power Tools](https://marketplace.visualstudio.com/items?itemName=EntityFrameworkTeam.EntityFrameworkPowerToolsBeta4).  As we are going to reverse engineer the `NorthWind` database, which is available from Microsoft [here](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/northwind-pubs). The reverse engineering feature allows us to take an existing database and generate classes for the Entity Framework from it.

To reverse engineer, right-click on the name of your solution and in the context menu choose: Entity Framework / Reverse Engineer Code First, as shown in the figure below:

<img src="https://www.carloscds.net/wp-content/uploads/2013/12/image_thumb13.png" width="859" height="271" alt="Reverse Engineer">

This command will open a window requesting information from the database. Complete with your information, in my case, the screen is as follows:

<img src="https://www.carloscds.net/wp-content/uploads/2013/12/SNAGHTML5372fbd_thumb2.png" width="477" height="737" alt="Connection Properties">

## Starting the Interception implementation

Now that we have the database mapped, let's implement the configuration class. Add a new class called `Configuration.cs` to the project, and we will put the code below:

```csharp
public class Configuration : DbConfiguration
{
    public Configuration()
    {            
    }
 }
```

That is the base class for the configuration. No code is needed to call this class, as the Entity Framework will identify it as long as it is in the same Assembly as the application, you do not need to call this class directly in any part of your code.

As we will intercept the commands sent to the database and we will generate a log, we will implement our class that will generate the logs. For this we will create another class called `InterceptorSQL`, see the code below:

```csharp
public class InterceptorSQL: IDbCommandInterceptor
{
    private string fileLog;

    public InterceptorSQL()
    {
        string caminho = Path.GetDirectoryName(Assembly.GetExecutingAssembly().GetModules()[0].FullyQualifiedName);
        arquivoLog = Path.Combine(caminho, "Log.txt");
    }
    
    private void FileLog(string s)
    {
        StreamWriter log = File.AppendText(arquivoLog);
        log.WriteLine(string.Format("{0} - {1}n", DateTime.Now.ToString(), s));
        log.Close();
    }
    
    public void NonQueryExecuting(DbCommand command, DbCommandInterceptionContext<int> interceptionContext)
    {
        Logger("NonQueryExecuting", command, interceptionContext);
    }

    public void NonQueryExecuted(DbCommand command, DbCommandInterceptionContext<int> interceptionContext)
    {
        Logger("NonQueryExecuted", command, interceptionContext);
    }

    public void ReaderExecuting(DbCommand command, DbCommandInterceptionContext<DbDataReader> interceptionContext)
    {
        Logger("ReaderExecuting", command, interceptionContext);
    }

    public void ReaderExecuted(DbCommand command, DbCommandInterceptionContext<DbDataReader> interceptionContext)
    {
        Logger("ReaderExecuted", command, interceptionContext);
    }

    public void ScalarExecuting(DbCommand command, DbCommandInterceptionContext<object> interceptionContext)
    {
        Logger("ScalarExecuting", command, interceptionContext);
    }
 
    public void ScalarExecuted(DbCommand command, DbCommandInterceptionContext<object> interceptionContext)
    {
        Logger("ScalarExecuted", command, interceptionContext);
    }

    private void Logger<TResult>(string commandKind, DbCommand command, DbCommandInterceptionContext<TResult> interceptionContext)
    {
        if (interceptionContext.Exception != null)
        {
            FileLog(string.Format("ERROR - Command SQL: {0},  Exception: {1}", command.CommandText, interceptionContext.Exception));
        }
        else
        {
            FileLog(string.Format("{0}: {1}", commandKind, command.CommandText));
        }
    }
}
```

In this class, we are implementing all the methods of the `IDbCommandInterceptor` interface as well as our method for generating the text file with the log (lines 11 to 16). In this example, I chose to write the log file in the same directory as the application, and for that, I took the directory inside the assembly using reflection (line 7). Recording the log is very simple, and we store the date/time, the type of operation, and the command executed. You can modify the `Logger` method and handle any other information you find necessary.

## Enabling Interception

Now that we have implemented our class to intercept commands, we need to inform the Entity Framework of the existence of this class. For this, let's go back to our `Configuration.cs` class and add the code below to the constructor:

```csharp
public class Configuration : DbConfiguration
{
    public Configuration()
    {
        this.AddInterceptor(new InterceptorSQL());
    }
}
```

The `AddInterceptor()` method tells the Entity Framework that we will intercept the operations described in the `InterceptorSQL` class.

## Testing the implementation

To test our class, let's perform some operations in the Entity Framework. See below the code we use in Program.cs:

```csharp
static void Main(string[] args)
{
    var db = new NorthwindContext();

    foreach(var p in db.Products)
    {
        Console.WriteLine(p.Category.CategoryName + ", " + p.ProductName);
    }

    var product = db.Products.Where(p => p.ProductID == 1).FirstOrDefault();
    
    if(product != null)
    {
        product.UnitPrice = 10;
        db.SaveChanges();
    }
}
```

When executing the program, the following `Log.txt` file is generated (only a part of the file was pasted here):

```console
29/12/2013 23:51:38 - ReaderExecuting: select serverproperty('EngineEdition')
 
29/12/2013 23:51:38 - ReaderExecuted: select serverproperty('EngineEdition')

29/12/2013 23:51:39 - ReaderExecuting: SELECT 
    [Extent1].[ProductID] AS [ProductID], 
    [Extent1].[ProductName] AS [ProductName], 
    [Extent1].[SupplierID] AS [SupplierID], 
    [Extent1].[CategoryID] AS [CategoryID], 
    [Extent1].[QuantityPerUnit] AS [QuantityPerUnit], 
    [Extent1].[UnitPrice] AS [UnitPrice], 
    [Extent1].[UnitsInStock] AS [UnitsInStock], 
    [Extent1].[UnitsOnOrder] AS [UnitsOnOrder], 
    [Extent1].[ReorderLevel] AS [ReorderLevel], 
    [Extent1].[Discontinued] AS [Discontinued]
    FROM [dbo].[Products] AS [Extent1]

29/12/2013 23:51:39 - ReaderExecuted: SELECT 
    [Extent1].[ProductID] AS [ProductID], 
    [Extent1].[ProductName] AS [ProductName], 
    [Extent1].[SupplierID] AS [SupplierID], 
    [Extent1].[CategoryID] AS [CategoryID], 
    [Extent1].[QuantityPerUnit] AS [QuantityPerUnit], 
    [Extent1].[UnitPrice] AS [UnitPrice], 
    [Extent1].[UnitsInStock] AS [UnitsInStock], 
    [Extent1].[UnitsOnOrder] AS [UnitsOnOrder], 
    [Extent1].[ReorderLevel] AS [ReorderLevel], 
    [Extent1].[Discontinued] AS [Discontinued]
    FROM [dbo].[Products] AS [Extent1]
```

## Conclusion

The Entity Framework command interception feature can be very useful for you to debug and analyze the commands sent to the database, including performance tests, since we record the start/end date and time.

Of course, this is a much more in-depth log than I already showed in this [post](/articles/carloscds-ef6-logging-sql-commands). Still, the main idea is to understand the new Entity Framework configuration mechanism, which opens up many possibilities, including the one we just explored.