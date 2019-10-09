---
PermaID: 100003
Name: CLR Stored Procedures
---

# CLR Stored Procedures

CLR is Common Language Runtime, and Stored Procedures are routine stored procedures of the database. Thus, CLR Stored Procedures are a combination of both.

 - CLR Stored Procedures are .NET objects which run in the memory of the database.
 - It is a managed object unlike Extended Stored Procedures, which are unmanaged objects. 

## Advantages

 - CLR Stored Procedures take benefit of .NET classes and make it easy to implement complex logic and calculation, etc. that are difficult to obtain in standard stored procedures. 
 - Standard stored procedures are best for data-oriented tasks, and CLR Stored Procedures not only include stored procedures but also include Functions, Triggers, etc. 
 - CLR Stored Procedures are managed codes so ensures type safety, memory management, etc.

## Requirements

In CLR, stored procedures are implemented as public static methods on a class in a Microsoft.NET Framework assembly. 

 - If the method takes parameters, the number of parameters in the .NET Framework implementation should be the same as the number of parameters used in the Transact-SQL declaration of the stored procedure.
 - Parameters passed to a CLR stored procedure can be any of the native SQL Server types that have an equivalent in managed code. 

Here is a simple CLR stored procedure which will print the text message.

```csharp
[Microsoft.SqlServer.Server.SqlProcedure]
public static void PrintText()
{
    SqlPipe sqlP = SqlContext.Pipe;
    sqlP.Send("This is a first stored procedure using CLR database object");
}
```

The `CREATE PROCEDURE` statement will create the stored procedure using the **SqlCLRDemo** assembly.

```sql
CREATE PROCEDURE PrintText
AS EXTERNAL NAME SqlClrDemo.StoredProcedures.PrintText
```

## Passing Parameters

You can also pass parameters to CLR stored procedure. The following method takes two parameters, the string data passed in the first parameter with additional string data will be returned as an output.

```csharp
[Microsoft.SqlServer.Server.SqlProcedure]
public static void PrintTextByPassingParams(string strInParam, out string strOutParam)
{
    strOutParam = $"Hi '{strInParam}', this is a stored procedure with parameters using CLR database object.";
}
```

The following `CREATE PROCEDURE` statement will create the `PrintTextByPassingParams` stored procedure which takes two parameters.

```sql
CREATE PROCEDURE dbo.PrintTextByPassingParams
    (
     @strInParam NVARCHAR (MAX) NULL,
     @strOutParam NVARCHAR (MAX) NULL OUTPUT
    )
AS EXTERNAL NAME SqlClrDemo.StoredProcedures.PrintTextByPassingParams
```

You can also use the **Publish** option to create the stored procedure automatically. The following example will execute `PrintTextByPassingParams`.

```sql
DECLARE @message nvarchar(max)
EXEC dbo.PrintTextByPassingParams @strInParam = 'Andy', @strOutParam = @message OUTPUT;

SELECT @message;
```

The expected output of the above example is as follows.

> Hi 'Andy', this is a stored procedure with parameters using CLR database object.