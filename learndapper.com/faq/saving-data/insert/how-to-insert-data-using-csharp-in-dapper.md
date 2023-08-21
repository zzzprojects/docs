---
id: 942dc0ac-8114-48e0-a83a-4958cdedab5e
position: 8
title: How to insert data using C# in Dapper?
---

Here is an example of inserting data using C# syntax:

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var sql = "INSERT INTO Customers (Name, Email) VALUES (@Name, @Email)";

	var customer = new Customer() { Name = "Learn Dapper", Email = "learndapper@example.com" };

	connection.Execute(sql, customer);
}
```