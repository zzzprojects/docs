---
id: 1aff9a2d-0d6a-4b33-aa9e-ce0b99f9fcf5
position: 7
title: How to insert data using if not exists in Dapper?
---

To insert data using if not exists, you have 2 choices:

1. Use the `Execute` method and `INSERT` with a `WHERE NOT EXISTS` sql statement
2. Use [Dapper Plus](https://dapper-plus.net/) with the `InsertIfNotExists` option

**Solution 1**
```csharp
using (var connection = new SqlConnection(My.connectionString))
{
	var sql = @"
INSERT INTO Customers (Name, Email)
SELECT @Name, @Email
WHERE NOT EXISTS (SELECT 1 FROM Customers AS X
	  WHERE X.CustomerID = @CustomerID)
";

	var customer = new Customer() { CustomerID = 13, Name = "Learn Dapper", Email = "learndapper@example.com" };

	connection.Execute(sql, customer);

	var insertedCustomers = connection.Query<Customer>("SELECT * FROM Customers").ToList();
}
```

**Solution 2**
```csharp
using (var connection = new SqlConnection(connectionString))
{
	var customer = new Customer() { CustomerID = 13, Name = "Learn Dapper", Email = "learndapper@example.com" };

	connection.UseBulkOptions(x => x.InsertIfNotExists = true).BulkInsert(customer);
	
	var insertedCustomers = connection.Query<Customer>("SELECT * FROM Customers").ToList();
}
```