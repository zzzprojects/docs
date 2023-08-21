---
id: 665a4420-69eb-469a-a841-052e8e03a473
position: 3
title: How to insert data including the identity value in Dapper?
---

To insert data, including the identity value, you have 2 choices:

1. Use the `Execute` method and turn on the [identity insert](https://learn.microsoft.com/en-us/sql/t-sql/statements/set-identity-insert-transact-sql?view=sql-server-ver16)
2. Use [Dapper Plus](https://dapper-plus.net/) with the `InsertKeepIdentity` option

**Solution 1**
```csharp
using (var connection = new SqlConnection(connectionString))
{
	var sql = @"
SET IDENTITY_INSERT Customers ON  

INSERT INTO Customers (CustomerID, Name, Email) VALUES (@CustomerID, @Name, @Email)
";

	var customer = new Customer() { CustomerID = 13, Name = "Learn Dapper", Email = "learndapper@example.com" };

	connection.Execute(sql, customer);

	Console.WriteLine(customer.CustomerID);
}
```

**Solution 2**
```csharp
// global context mapping: https://dapper-plus.net/getting-started-mapping#global-context-mapping
DapperPlusManager.Entity<Customer>().Identity(x => x.CustomerID);

using (var connection = new SqlConnection(connectionString))
{
	var customer = new Customer() { CustomerID = 13, Name = "Learn Dapper", Email = "learndapper@example.com" };

	connection.UseBulkOptions(x => x.InsertKeepIdentity = true).BulkInsert(customer);

	Console.WriteLine(customer.CustomerID);
}
```