---
title: String Parameters
description: Dapper string parameterization helps to avoid SQL injection attacks in applications that use dynamic SQL. It also makes the code more readable by replacing raw SQL strings with parameters, which are visible and can be used for debugging purposes.
canonical: /string-parameters
status: 
lastmod: 
---

# String Parameters

Using the dapper string parameter feature is a great way to protect against SQL injection. It allows developers to pass parameters directly into their queries without having to worry about manually sanitizing user input. 

 - Each parameter is assigned a name and can be populated with data at runtime. 
 - It helps ensure that only expected values are used in the query and that malicious code can't be inserted. 
 - Dapper also makes it easy to create stored procedures that accept multiple parameters, making it easier to build secure and efficient applications. 

Dapper provides various extension methods which allow you to use the `DbString` parameter in the query. The example below demonstrates how to use it. 

```csharp
string sql = "SELECT * FROM Customers WHERE CustomerId = :CustomerId";

using (var connection = My.ConnectionFactory())
{
	connection.Open();

	var dbParams = new DbString()
	{
		Value = customerId, 
		IsAnsi=true, 
		IsFixedLength=true 
	};

	var customer = connection.Query<Customer>(sql, new { CustomerId = dbParams })
		.FirstOrDefault();
}
```

As you can see, we created a `DbString` object with the corresponding `customerId` and set it as the parameter for the query. The `IsAnsi` and `IsFixedLength` properties are used to determine the type of string we are using. We then use this parameter as part of our query, which in this case will select the customer from the database with the corresponding `CustomerId` value. 

Overall, using the dapper string parameter feature is a great choice for any .NET developer looking to create highly secure and efficient applications. 

