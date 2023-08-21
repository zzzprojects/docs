---
id: 4cc7f8e0-795d-479c-a274-db6d81396f31
position: 2
title: How to insert data and return the id in Dapper?
---

To insert data and return the id, you have 2 choices:

1. Use the `Execute` method and select the identity inserted with [SCOPE_IDENTITY()](https://learn.microsoft.com/en-us/sql/t-sql/functions/scope-identity-transact-sql)
2. Use [Dapper Plus](https://dapper-plus.net/) with the property mapped as identity or output

**Solution 1**
```csharp
using (var connection = new SqlConnection(connectionString))
{
	var sql = @"
INSERT INTO Customers (Name, Email) VALUES (@Name, @Email)
SELECT SCOPE_IDENTITY()
";

	var customer = new Customer() { Name = "Learn Dapper", Email = "learndapper@example.com" };

	var customerID = connection.Execute(sql, customer);
	customer.CustomerID = customerID;

	Console.WriteLine(customer.CustomerID);
}
```

**Solution 2**
```csharp
// global context mapping: https://dapper-plus.net/getting-started-mapping#global-context-mapping
DapperPlusManager.Entity<Customer>().Identity(x => x.CustomerID);

using (var connection = new SqlConnection(connectionString))
{
	var customer = new Customer() { Name = "Learn Dapper", Email = "learndapper@example.com" };

	connection.BulkInsert(customer);

	Console.WriteLine(customer.CustomerID);
}
```