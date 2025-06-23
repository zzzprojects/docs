---
Permalink: command-interception-in-ef-core
Name: What is Command Interception
LastMod: 2025-06-23
---

# What is Command Interception

The high-level goal for the interception feature is to allow external code to observe and potentially intercept EF operations. 

 - Anytime Entity Framework sends a command to the database this command can be intercepted by application code.
 - Using this approach, you can capture a lot more information transiently without having to mess up your code.
 - EF6 provides a dedicated logging API that can make it easier to do logging. 
 - In this article, we will cover how to use the Entity Framework's interception feature directly for logging.

## Command Interception In EF Core

Right now, Entity Framework Core still does not have all the features provided in Entity Framework 6 including the command interception. To use command interception in EF Core, you can use [Entity Framework Extensions](https://entityframework-extensions.net/) which provides `DbCommandInterceptor`.

To get this feature in your EF Core, install the [Z.EntityFramework.Extensions.EFCore](https://www.nuget.org/packages/Z.EntityFramework.Extensions.EFCore/) NuGet package or run the following command in **Package Manager Console**.

```csharp
PM> Install-Package Z.EntityFramework.Extensions.EFCore
```

The DbCommandInterceptor in EF Core is very similar to EF6. 

 - You will need to create a class that implements DbCommandInterceptor.
 - You will also need to override the virtual methods that you want to intercept 
 - All methods are available with the same signatures. 
 - It can do pretty much everything as it can in EF6. 

## Create Interceptor

To implement command interception, we need to create a custom interceptor and register it accordingly. Entity Framework will call it whenever a query is sent to the database.

Let's create a new class `EFCommandInterceptor` that implements `DbCommandInterceptor` class which is available in [Z.EntityFramework.Extensions.EFCore](https://www.nuget.org/packages/Z.EntityFramework.Extensions.EFCore/).

```csharp
public class EFCommandInterceptor : DbCommandInterceptor
{
    public override void NonQueryExecuted(DbCommand command, DbCommandInterceptionContext<int> interceptionContext)
    {
        base.NonQueryExecuted(command, interceptionContext);
        LogInfo("EFCommandInterceptor.NonQueryExecuted", interceptionContext.Result.ToString(), command.CommandText);
    }

    public override void NonQueryExecuting(DbCommand command, DbCommandInterceptionContext<int> interceptionContext)
    {
        base.NonQueryExecuting(command, interceptionContext);
        LogInfo("EFCommandInterceptor.NonQueryExecuting", interceptionContext.EventData.ToString(), command.CommandText);
    }

    public override void ReaderExecuted(DbCommand command, DbCommandInterceptionContext<DbDataReader> interceptionContext)
    {
        base.ReaderExecuted(command, interceptionContext);
        LogInfo("EFCommandInterceptor.ReaderExecuted", interceptionContext.Result.ToString(), command.CommandText);
    }

    public override void ReaderExecuting(DbCommand command, DbCommandInterceptionContext<DbDataReader> interceptionContext)
    {
        base.ReaderExecuting(command, interceptionContext);
        LogInfo("EFCommandInterceptor.ReaderExecuting", interceptionContext.EventData.ToString(), command.CommandText);
    }

    public override void ScalarExecuted(DbCommand command, DbCommandInterceptionContext<object> interceptionContext)
    {
        base.ScalarExecuted(command, interceptionContext);
        LogInfo("EFCommandInterceptor.ScalarExecuted", interceptionContext.Result.ToString(), command.CommandText);
    }

    public override void ScalarExecuting(DbCommand command, DbCommandInterceptionContext<object> interceptionContext)
    {
        base.ScalarExecuting(command, interceptionContext);
        LogInfo("EFCommandInterceptor.ScalarExecuting", interceptionContext.EventData.ToString(), command.CommandText);
    }

    public override void NonQueryError(DbCommand command, DbCommandInterceptionContext<int> interceptionContext, Exception exception)
    {
        base.NonQueryError(command, interceptionContext, exception);
        LogInfo("EFCommandInterceptor.NonQueryError", interceptionContext.EventData.ToString(), command.CommandText, exception.Message);
    }

    public override void ReaderError(DbCommand command, DbCommandInterceptionContext<DbDataReader> interceptionContext, Exception exception)
    {
        base.ReaderError(command, interceptionContext, exception);
        LogInfo("EFCommandInterceptor.NonQueryError", interceptionContext.EventData.ToString(), command.CommandText, exception.Message);
    }

    public override void ScalarError(DbCommand command, DbCommandInterceptionContext<object> interceptionContext, Exception exception)
    {
        base.ScalarError(command, interceptionContext, exception);
        LogInfo("EFCommandInterceptor.NonQueryError", interceptionContext.EventData.ToString(), command.CommandText, exception.Message);
    }

    private void LogInfo(string method, string command, string commandText)
    {
        Console.WriteLine("Intercepted on: {0} \n {1} \n {2}", method, command, commandText);
    }

    private void LogInfo(string method, string command, string commandText, string exception)
    {
        Console.WriteLine("Intercepted on: {0} \n {1} \n {2} \n {3}", method, command, commandText, exception);
    }
}
```

This code writes commands and queries on the Console Window. The `DbCommandInterceptionContext` currently has the following properties: 

 - DbContext
 - Result (populated only on "Executed" event) 
 - EventData which contains all information about this event.

## Register Interceptor

Once a class that implements the interception has been created it can be registered using the `DbInterception` class as shown below. 

```csharp
DbInterception.Add(new EFCommandInterceptor());
```

You can add interceptors using the `DbInterception.Add` method anywhere in your code such as, `Application_Start` method or in the `DbConfiguration` class, etc.

 - Be careful not to execute `DbInterception.Add` for the same interceptor more than once, otherwise, you will get additional interceptor instances. 
 - For example, if you add the logging interceptor twice, you will see two logs for every SQL query.
 - In this example, we will register the interceptor in the `main` method.

You can also bind the interceptor to the context if you want to have information about context, but this step is optional.

```csharp

public static EFCommandInterceptor CommandInterceptor = new EFCommandInterceptor();

public class CurrentContext : DbContext
{
    public CurrentContext()
    {
        this.BindInterceptor(CommandInterceptor);
    }

    public DbSet<Customer> Customers { get; set; }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
        optionsBuilder.UseSqlServer(@"Data Source=(localdb)\ProjectsV13;Initial Catalog=TestDB;");
    }
}
```

Now let's insert some data to the database and then retrieve the data from the database.

```csharp
static void Main(string[] args)
{
    DbInterception.Add(CommandInterceptor);

    using (var context = new CurrentContext())
    {
        var list = new List<Customer>();

        list.Add(new Customer() { Name = "Customer_A", IsActive = true });
        list.Add(new Customer() { Name = "Customer_B", IsActive = true });
        list.Add(new Customer() { Name = "Customer_C", IsActive = true });

        context.Customers.AddRange(list);
        context.SaveChanges();
    }

    using (var context = new CurrentContext())
    {
        var list = context.Customers.ToList();
    }
}
```

Let's run your application in debug mode, and you will see all the commands on the console window.

```csharp
Intercepted on: EFCommandInterceptor.ReaderExecuting
 Executing DbCommand [Parameters=[@p0='?' (DbType = Boolean), @p1='?' (Size = 4000)], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
INSERT INTO [Customers] ([IsActive], [Name])
VALUES (@p0, @p1);
SELECT [CustomerID]
FROM [Customers]
WHERE @@ROWCOUNT = 1 AND [CustomerID] = scope_identity();
 SET NOCOUNT ON;
INSERT INTO [Customers] ([IsActive], [Name])
VALUES (@p0, @p1);
SELECT [CustomerID]
FROM [Customers]
WHERE @@ROWCOUNT = 1 AND [CustomerID] = scope_identity();


Intercepted on: EFCommandInterceptor.ReaderExecuted
 System.Data.SqlClient.SqlDataReader
 SET NOCOUNT ON;
INSERT INTO [Customers] ([IsActive], [Name])
VALUES (@p0, @p1);
SELECT [CustomerID]
FROM [Customers]
WHERE @@ROWCOUNT = 1 AND [CustomerID] = scope_identity();


Intercepted on: EFCommandInterceptor.ReaderExecuting
 Executing DbCommand [Parameters=[@p0='?' (DbType = Boolean), @p1='?' (Size = 4000)], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
INSERT INTO [Customers] ([IsActive], [Name])
VALUES (@p0, @p1);
SELECT [CustomerID]
FROM [Customers]
WHERE @@ROWCOUNT = 1 AND [CustomerID] = scope_identity();
 SET NOCOUNT ON;
INSERT INTO [Customers] ([IsActive], [Name])
VALUES (@p0, @p1);
SELECT [CustomerID]
FROM [Customers]
WHERE @@ROWCOUNT = 1 AND [CustomerID] = scope_identity();


Intercepted on: EFCommandInterceptor.ReaderExecuted
 System.Data.SqlClient.SqlDataReader
 SET NOCOUNT ON;
INSERT INTO [Customers] ([IsActive], [Name])
VALUES (@p0, @p1);
SELECT [CustomerID]
FROM [Customers]
WHERE @@ROWCOUNT = 1 AND [CustomerID] = scope_identity();


Intercepted on: EFCommandInterceptor.ReaderExecuting
 Executing DbCommand [Parameters=[@p0='?' (DbType = Boolean), @p1='?' (Size = 4000)], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
INSERT INTO [Customers] ([IsActive], [Name])
VALUES (@p0, @p1);
SELECT [CustomerID]
FROM [Customers]
WHERE @@ROWCOUNT = 1 AND [CustomerID] = scope_identity();
 SET NOCOUNT ON;
INSERT INTO [Customers] ([IsActive], [Name])
VALUES (@p0, @p1);
SELECT [CustomerID]
FROM [Customers]
WHERE @@ROWCOUNT = 1 AND [CustomerID] = scope_identity();


Intercepted on: EFCommandInterceptor.ReaderExecuted
 System.Data.SqlClient.SqlDataReader
 SET NOCOUNT ON;
INSERT INTO [Customers] ([IsActive], [Name])
VALUES (@p0, @p1);
SELECT [CustomerID]
FROM [Customers]
WHERE @@ROWCOUNT = 1 AND [CustomerID] = scope_identity();


Intercepted on: EFCommandInterceptor.ReaderExecuting
 Executing DbCommand [Parameters=[], CommandType='Text', CommandTimeout='30']
SELECT [c].[CustomerID], [c].[IsActive], [c].[Name]
FROM [Customers] AS [c]
 SELECT [c].[CustomerID], [c].[IsActive], [c].[Name]
FROM [Customers] AS [c]
Intercepted on: EFCommandInterceptor.ReaderExecuted
 System.Data.SqlClient.SqlDataReader
 SELECT [c].[CustomerID], [c].[IsActive], [c].[Name]
FROM [Customers] AS [c]
```

## Limitations

 - Unlike EF6, you cannot change the result. However, you can still access DbDataReader and read the result.
 - If the connection is not unique by context, the interceptor will always return the context that was the last bind for this shared connection.
 - Connection is usually unique by context, otherwise, it will have some concurrency issue with opening/closing the connection.
