---
title: Dapper Parameter, SQL Injection, Anonymous and Dynamic Parameters
description: Dapper allows specifying parameters in querying methods to avoid SQL Injection. Learn more about how to use anonymous, dynamic, string, and output parameter
canonical: /parameters
status: Published
lastmod: 2023-01-05T12:53:34Z
---

# Using Parameters With Dapper

The dapper parameters work by allowing you to create an object that contains all of the values and parameter types you wish to use in your Dapper query. By doing this, Dapper can automatically map each value and parameter type to fields in your database table. 

 - Dapper parameters also help to prevent SQL injection, as the dapper will automatically recognize any attempted malicious code and throw an exception instead of executing it. 
 - Using dapper parameters is a great way to ensure that your applications are secure from malicious attacks while still providing an easy and efficient way to insert data into your Dapper queries. 

Parameters are represented in the SQL command by placeholders, and the values are passed to the command within the `DbCommand` object's `Parameters` collection. The format of the placeholder is dependent on what the provider supports. For example, the `SqlClient` provider supports named parameters, with the parameter name prefixed with an `@` character. The `OleDb` provider supports positional parameters. Values are passed to the SQL command based on matching the order in which they have been added to the parameters collection to the order in which placeholders appear in the SQL. Parameter placeholders can be named anything as long as the placeholder names don't match database object names (columns, tables, etc.).

When constructing a parameterized query with Dapper, you can use either named or anonymous parameters. Named parameters are used when creating a query where each parameter has its unique name. That can help make the query easier to read and debug if something goes wrong. Anonymous parameters are used when creating a query where all parameters should be treated the same way, such as when passing in a set of values for an IN clause or when using parameterized queries with stored procedures.

## Why You Should Use Parameters?

Parameters allow developers to quickly and easily create parameterized SQL queries, which makes it safer to query your database directly with user input. Working with parameters in Dapper is relatively easy, but you should know a few things to get the most out of it. 

 - When creating a parameterized query, it is essential to use the correct data type when binding values. 
 - For example, if your column is an integer, you should use an int or `Int32` rather than a string. 
 - It ensures that the database will handle the value as the correct data type, making it less likely that an error will occur. 
 - It also helps increase performance by ensuring that only valid values are passed to the database.

### What is SQL Injection?

SQL Injection is a hacking technique that takes advantage of applications or websites that allow user input without validating it first. Hackers can submit malicious input data, such as SQL statements, to disrupt the normal flow and behavior of the application or website. By manipulating the SQL statement, hackers can gain access to unauthorized data, tamper with the data in the database, or even delete the entire database.

The most common example of SQL Injection is a login form. Usually, when users enter their username and password into the login form, they are verified against what is stored in the database. But if malicious input is submitted instead, it can alter the intended SQL statement and provide the hacker access to the system. 

It is important for developers to validate user input and use properly parameterized queries and stored procedures to prevent this type of attack. That will ensure that the application or website is secure from SQL injection attacks. Additionally, security teams should regularly audit their systems for any suspicious activity and ensure all patches and updates are applied when available. For example, a vulnerable SQL statement designed to check that submitted credentials against those stored in the database might look like this:

```csharp
var sql = "SELECT * FROM users WHERE username = '" + username + "' AND password = '" + password + "'";
```

The `username` and `password` variables represent the values submitted by the user. They are concatenated with the SQL to generate the command that is executed. If those values are `"mike"` and `"pass1"` respectively, the generated SQL will be: 

```sql
SELECT * FROM users WHERE username = 'mike' AND password = 'pass1'
```
When executed, the command should only return rows if a match for those values is found. However, what happens if the value submitted for the password is `' or ''='`? The resulting SQL will become:

```sql
SELECT * FROM users WHERE username = 'mike' AND password = '' or ''=''
```
That completely alters the effect of the SQL so that it returns all rows in the user table because `''=''` is always true. Additional SQL syntax has been injected into the statement to change its behavior. The single quotes are string delimiters as far as T-SQL is concerned, and if you allow users to enter these without managing them, you are asking for potential trouble.

Often, you will see well-meaning advice from people that you should escape single quotes, which converts them to literal values instead of SQL syntax:

```csharp
username = username.Replace("'","''");
password = password.Replace("'","''");
var sql = "SELECT * FROM users WHERE username = '" + username + "' AND password = '" + password + "'";
```

Indeed, this has the desired effect. The generated SQL will no longer return all rows in the database. However, this does not mitigate against all forms of SQL injection. For example, consider the common scenario where you are querying the database for an article, product, or similar by `ID`. Typically, the `ID` is stored as a number - most of them are autogenerated by the database:

```csharp
var sql = "SELECT * FROM products WHERE productid = " + productid;
```
The value for the `productid` variable could come from a posted form or a query string value - perhaps from a hyperlink on a previous page. Unfortunately, it's easy for a malicious user to amend a query string value. 

Imagine that the malicious user appends `; drop table AspNetUsers--` on the end of the query string before requesting the page. Now the generated SQL becomes

```sql
SELECT * FROM products WHERE productid = 1;drop table AspNetUsers--
```
Any database that can manage batch commands will execute this, and the _AspNetUsers_ table will disappear.

Some people advise that you should validate all user input against a blacklist of SQL keywords and syntax. Still, the problem with this approach is that there are perfectly valid reasons for users to submit values that might include entries in the blacklist. In addition, you will have to go back and patch your blacklist every time your database provider adds new keywords to their product as they improve their feature set.

## Dapper Anonymous Parameters

Anonymous Parameters provide a powerful way of executing SQL queries without hard coding the parameter names in your query string. By using anonymous parameters, you can ensure security is upheld, and SQL injection attacks are avoided. Furthermore, using the `Execute` method with anonymous parameters makes it easy to write complex queries in one go, eliminating the need for creating various classes to represent data. That not only saves time and effort but also makes the overall development process more efficient. With its easy-to-use parameter support, Dapper makes it simple and safe to use parameters in your queries. 

When using anonymous parameters with Dapper, you can be confident that your code is secure and up-to-date with the most modern database query techniques. With the ability to quickly execute complex queries without hard coding, you'll save time and effort on development tasks while ensuring that data is safe from malicious attacks. Whether you're new to SQL or an experienced user, Dapper's Anonymous Parameter support will make your work easier and more efficient.

The following example shows how the parameter values can be passed to commands as anonymous types:

```csharp
var parameters = new { UserName = username, Password = password };
var sql = "SELECT * from users where username = @UserName and password = @Password";
var result = connection.Query(sql, parameters);
```

## Dapper Dynamic Parameters

Dapper also provides a `DynamicParameters` class, representing a "bag" of parameter values. 

Dynamic Parameters create a dynamic object that can be passed to the `Query` method instead of normal parameters. That is done using the `DynamicParameters` C# class, which has several methods for adding and setting parameters. Once you have filled in all your dynamic parameters, they can be passed to the query as an argument.

Dapper will then take care of parsing and constructing the SQL query for you. That makes it easier and safer to work with dynamic parameters, as all of the parameter handlings is taken care of by Dapper.

You can pass an object to its constructor. Suitable objects include a `Dictionary<string, object>`:

```csharp
var dictionary = new Dictionary<string, object>
{
    { "@ProductId", 1 }
};
var parameters = new DynamicParameters(dictionary);
var sql = "SELECT * FROM products WHERE ProductId = @ProductId";
using (var connection = new SqlConnection(connectionString))
{
    var product = connection.QuerySingle<Product>(sql, parameters);
}
```

Or you can pass an anonymous type:
```csharp
var parameters = new DynamicParameters({ ProductId = 1 });
var sql = "SELECT * FROM products WHERE ProductId = @ProductId";
using (var connection = new SqlConnection(connectionString))
{
    var product = connection.QuerySingle<Product>(sql, parameters);
}
```
Or you can pass a stub representing the object that you are looking to return:

```csharp
var template = new Product { ProductId = 1 };
var parameters = new DynamicParameters(template);
var sql = "SELECT * FROM products WHERE ProductId = @ProductId";
using (var connection = new SqlConnection(connectionString))
{
    var product = connection.QuerySingle<Product>(sql, parameters);
}
```

The `DynamicParameters` type provides an `Add` method, enabling you to pass explicit parameters, specifying the datatype, direction, and size:

```csharp
var parameters = new DynamicParameters();
var customerId = "ALFKI";
parameters.Add("@CustomerId", customerId, DbType.String, ParameterDirection.Input, customerId.Length);
var sql = "SELECT * FROM customers WHERE CustomerId = @CustomerId";
using (var connection = new SqlConnection(connectionString))
{
    var customer = connection.QuerySingle<Customer>(sql, parameters);
}
``` 

:::{.alert .alert-warning}
Some ORMs [permit the use of format strings or interpolation](https://www.learnentityframeworkcore.com/raw-sql#parameterized-queries) when building SQL strings dynamically. Dapper doesn't support this approach at all. You should always pass parameter values using one of the approaches above. 
:::

## Dapper String Parameters

The Dapper string parameter feature is a great way to protect against SQL injection. It allows developers to pass parameters directly into their queries without worrying about manually sanitizing user input. 

 - Each parameter is assigned a name and can be populated with data at runtime. 
 - It helps ensure that only expected values are used in the query and that malicious code can't be inserted. 
 - Dapper also makes it easy to create stored procedures that accept multiple parameters, making it easier to build secure and efficient applications. 

Dapper provides various extension methods which allow you to use the `DbString` parameter in the query. The example below demonstrates how to use it. 

```csharp
string sql = "SELECT * FROM Customers WHERE Email = @Email";

using (var connection = new SqlConnection(connectionString))
{
	connection.Open();

	var dbParams = new DbString()
	{
		Value = Email, 
		IsAnsi=true, 
		IsFixedLength=true 
	};

	var customer = connection.Query<Customer>(sql, new { Email = dbParams })
		.FirstOrDefault();
}
```

As you can see, we created a `DbString` object with the corresponding `Email` and set it as the parameter for the query. The `IsAnsi` and `IsFixedLength` properties are used to determine the type of string we are using. We then use this parameter as part of our query, which in this case, will select the customer from the database with the corresponding `Email` value. 

Overall, using the dapper string parameter feature is an excellent choice for any .NET developer looking to create highly secure and efficient applications. 

## Dapper WHERE IN Parameters

Using dapper, you can use the 'WHERE IN' clause to query a list of values. That can be accomplished by utilizing the dapper's anonymous type parameter. To do this, you must pass an anonymous type object as a parameter and assign each value a unique name. 

For example, if we are querying a table based on a list of IDs, we could pass a dapper parameter like this: 

```csharp
var dapperParams = new { param1 = value1, param2 = value2 ...};
```

Then in our SQL statement, we would use `WHERE IN (param1, param2...)` to query the table based on the values provided in `dapperParams`. 

```csharp
var ids = new[] { 3, 7, 12 };

var sql = "SELECT * FROM Products WHERE ProductId IN @Ids;";

using (var connection = new SqlConnection(connectionString))
{
	connection.Open();

	var products = connection.Query<Product>(sql, new {Ids = ids }).ToList();
}
```

By using dapper's anonymous type parameter feature, we can easily pass multiple values to our SQL query and get the desired results.

## Dapper Table-Valued Parameters

Dapper allows you to pass Table-valued parameters (TVP) to SQL Server. That is useful when sending a set of data through the database layer, such as when performing bulk inserts/updates. TVPs have been around since version 2008 and offer better performance than traditional methods of sending data in batches. 

To use TVPs with Dapper, you must create a custom type and pass it to the stored procedure. The example below shows how this can be done. First, we need to create a user-defined type in the database: 

```csharp
CREATE TYPE TvpExampleType AS TABLE 
(
    ID INT, 
    Name VARCHAR(50)
)
```

Then we create a stored procedure that accepts this type as an argument: 

```csharp
CREATE PROCEDURE dbo.MyStoredProc 
    (@TvpExampleType TvpExampleType READONLY) 
AS 
BEGIN 
    --Do something 
END
```

Finally, we can set up our C# code to use the TVP: 

```csharp
var tvpExampleType = new List<TvpExampleType>(); 
tvpExampleType.Add(new TvpExampleType {ID = 1, Name = "John" }); 
tvpExampleType.Add(new TvpExampleType {ID = 2, Name = "Steve" }); 
 
var p = new DynamicParameters(); 
p.Add("TvpExampleType", tvpExampleType.AsTableValuedParameter("dbo.TvpExampleType")); 
await connection.ExecuteAsync("dbo.MyStoredProc", p, commandType: CommandType.StoredProcedure);
```

You now have a way to pass a list of data to your stored procedure using Dapper. TVPs are an efficient and powerful way to send data to SQL Server and can be used with Dapper easily. 

## Dapper Output Parameter

Dapper provides the ability to use output parameters in stored procedures and parameterized queries, allowing for greater control over the data being returned. 

 - Output parameters are declared using the same syntax as input parameters but with an additional `OUTPUT` keyword. 
 - When declaring an output parameter, it must be given a name (the "parameter name"), an optional data type, and an optional size.
 - To use dapper output parameters, you must first create a `DynamicParameters` object in your code and then add it as a parameter to your query. 

Let's consider we have a simple stored procedure that takes the id as input and returns the name and description as output parameters.

```csharp
Create Procedure GetCustomerDetails
   @CustomerID          INT,
   @Name                NVARCHAR(Max)         OUTPUT,
   @Description         NVARCHAR(Max)         OUTPUT

AS
   SELECT
      @Name=Name,
      @Description=Description FROM Customers
   WHERE CustomerID=@CustomerID

```

When executing the command, Dapper will handle setting and retrieving the parameter values for you, allowing you to manipulate them in your application easily. Once you have executed your query and set up the parameters as needed, Dapper will automatically map the output data to your defined object. 

To execute a query with output parameters, the `Execute` method must be used. It will return the number of affected rows and fill in the value of the output parameter from the database.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var p = new DynamicParameters();
    p.Add("@CustomerID", 1002);
    p.Add("@Name", null, dbType: DbType.String, direction: ParameterDirection.Output, 50);
    p.Add("@Description", null, dbType: DbType.String, direction: ParameterDirection.Output,50);
    
    connection.Execute("GetCustomerDetails", p, commandType: CommandType.StoredProcedure);
    
    var name = p.Get<string>("@Name");
    var description = p.Get<string>("@Description");
}
```

The value of the output parameter can then be accessed from the same parameter object. 

#### Advantages of Output Parameters

Using output parameters with Dapper allows developers to access data from databases safely and efficiently while taking full advantage of the power of stored procedures. Output parameters can also be used to return multiple resultsets or structured data in one go. 

The output parameters let you handle the results of a stored procedure. It allows you to change your output type and structure without modifying existing code. You can also use it to return multiple result sets or an output parameter with different types like `INT`, `VARCHAR`, and `DECIMAL`. 

That makes Dapper output parameters an extremely powerful tool that can help you quickly and easily create dynamic, complex queries with minimal effort. 