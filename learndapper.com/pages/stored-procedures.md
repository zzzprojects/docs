---
title: Dapper Stored Procedure
description: All Dapper querying methods allow executing stored procedures without writing extra code by specifying the commandType parameter to 'StoredProcedure'.
canonical: /stored-procedures
status: Published
lastmod: 2023-08-01
---

# Executing Stored Procedures With Dapper

Stored procedures are a great way to encapsulate business logic and reduce the complexity of handling multiple queries. It can also improve performance by optimizing query execution. 

 - Stored procedures also provide better security since they are isolated from the application code, making it harder to manipulate data outside of the stored procedure.
 - Stored procedures are also easier to maintain since they are centrally located and can be modified from one place. 
 - It helps reduce the chances of duplicate queries and makes it easier to identify errors quickly.

To demonstrate this feature, let's create a simple Customer table:

```csharp
CREATE TABLE [Customer]
(
    [CustomerID] [INT] IDENTITY(1,1) NOT NULL,
    [FirstName] [VARCHAR](100) NULL,
    [LastName] [VARCHAR](100) NULL,
    [BirthDate] [DATE] NULL
)
```

We will execute a stored procedure that takes an id parameter and returns the Customer with that particular id. Here is the stored procedure code:

```csharp
CREATE PROCEDURE GetCustomerById 
    (@id int)  
AS  
BEGIN    
    SELECT * FROM Customer WHERE CustomerID = @id  
END 
```

Now that we have the stored procedure created let's look at how to execute it using Dapper.

To begin with, we need to create a connection.

Next, we need to set up the parameters that will be used to pass values into the stored procedure. We can do this using a `DynamicParameters` object and adding each parameter's values. In this case, our parameter is an `id` of type `int`. 

Finally, we can execute the stored procedure using extend methods from Dapper, such as the `Execute` method and pass in our parameters. In our case, we used the [QuerySingleOrDefault](/dapper-query/selecting-single-rows) method to map the result to a `Customer` object containing all the data from the retrieved record. 
Here is an example of how this code might look:

```csharp
using(var connection = new SqlConnection(connectionString))
{
    //Set up DynamicParameters object to pass parameters  
    DynamicParameters parameters = new DynamicParameters();   
    parameters.Add("id", 1);  
	
    //Execute stored procedure and map the returned result to a Customer object  
    var customer = conn.QuerySingleOrDefault<Customer>("GetCustomerById", parameters, commandType: CommandType.StoredProcedure);
}
```

You have now successfully executed a stored procedure using Dapper. That example can be expanded to include more complex stored procedures, which may consist of multiple parameters and return a list of objects. 

There are two ways to execute stored procedures with Dapper: with the `CommandType` as `Text`; or as `StoredProcedure`. 

## Dapper CommandType.Text

All `DbCommand` objects have a `CommandType` property. By default, this is set to `Text`. If you want to execute a stored procedure in a SQL statement (text), you use the [Execute (or Exec) statement](https://docs.microsoft.com/en-us/sql/t-sql/language-elements/execute-transact-sql?view=sql-server-2017):

```csharp
var sql = "EXEC GetSalesByYear @BeginningDate, @EndingDate";
var values = new { BeginningDate = "2017-01-01", EndingDate = "2017-12-31" };
var results = connection.Query(sql, values).ToList();
results.ForEach(r => Console.WriteLine($"{r.OrderID} {r.Subtotal}"));
```

The name of the stored procedure, in this case, is **GetSalesByYear**. Parameters are supplied as a comma-separated list after the name of the procedure.

## Dapper CommandType.StoredProcedure

Dapper provides access to the `CommandType` property via the `commandType` parameter included in all the various [querying methods](/dapper-query) provided by Dapper. 

The next example is the equivalent to the above, but with the `CommandType` set to `StoredProcedure`:

```csharp
var storedProcedureName = "GetSalesByYear";
var values = new { BeginningDate = "2017-01-01", EndingDate = "2017-12-31" };
var results = connection.Query(storedProcedureName, values, commandType: CommandType.StoredProcedure).ToList();
results.ForEach(r => Console.WriteLine($"{r.OrderID} {r.Subtotal}"));
```

It's recommand to always set the command type to `CommandType.StoredProcedure` when using a stored procedure in Dapper for multiple reasons:

- **Code Clarity**: Using `CommandType.StoredProcedure` communicates the intent of your code more clearly.
- **Security**: By specifying `CommandType.StoredProcedure`, you are less susceptible to SQL injection attacks because the stored procedure name and parameters are specified separately.
- **Performance**: SQL Server can optimize the execution of stored procedures better than arbitrary SQL statements.
- **Parameter Handling**: Dapper can handle output and return parameters correctly when `CommandType.StoredProcedure` is used. If you use 'EXEC' with `CommandType.Text`, you would need to manually handle output and return parameters.

## Related Articles

- [Querying](/dapper-query)
- [Querying Scalar Values](/dapper-query/selecting-scalar-values)
- [Querying Multiple Results](/dapper-query/selecting-multiple-results)
- [Executing Non-Query](/non-query)
- [Using Parameters](/parameters)

