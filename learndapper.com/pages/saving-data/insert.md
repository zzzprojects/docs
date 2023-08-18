---
title: Dapper Insert, Insert Async, Multiple Rows, Bulk Insert
description: The Dapper Execute method allows you to insert single data or insert multiple rows. In addition, you can use Dapper Plus to BulkInsert data in your database.
canonical: /saving-data/insert
status: Published
lastmod: 2023-08-01
---

# Inserting Data With Dapper

To insert data in Dapper, you need to write your `INSERT` sql statement and call the [Execute](/non-query) method. Make sure to use [parameters](/parameters) when passing your values to avoid SQL Injection.

In this article, we will talk about the 4 most common scenarios for inserting:

- [Inserting single row](#dapper-insert)
- [Inserting single row asynchronously](#dapper-insert-async)
- [Inserting multiple rows](#dapper-insert-multiple-rows)
- [Bulk inserting rows](#dapper-bulkinsert)

## Dapper Insert

To insert in dapper, you need to use the [Execute](/non-query#dapper-execute) method with an `INSERT` statement and provide your query parameters values.

In this example:

1. We will create a connection
2. We will create an `INSERT` sql statement
3. Call the `Execute` method
   3a. The first time, we will pass parameters values with an anonymous type
   3b. The second time, we will pass parameters values by providing the customer entity

```csharp
// 1. We will create a connection
using (var connection = new SqlConnection(connectionString))
{
    // 2. We will create an `INSERT` sql statement
	var sql = "INSERT INTO Customers (Name, Email) VALUES (@Name, @Email)";

	// 3. Call the `Execute` method
	{
		// 3a. The first time, we will pass parameters values with an anonymous type
		var anonymousCustomer = new { Name = "ZZZ Projects", Email = "zzzprojects@example.com" };

		var rowsAffected = connection.Execute(sql, anonymousCustomer);
		Console.WriteLine($"{rowsAffected} row(s) inserted.");
	}

	{
		// 3b. The second time, we will pass parameters values by providing the customer entity
		var customer = new Customer() { Name = "Learn Dapper", Email = "learndapper@example.com" };

		var rowsAffected = connection.Execute(sql, customer);
		Console.WriteLine($"{rowsAffected} row(s) inserted.");
	}

	var insertedCustomers = connection.Query<Customer>("SELECT * FROM Customers").ToList();
}
```

## Dapper Insert Async

To insert in dapper asynchronously, you need to use the [ExecuteAsync](/non-query#dapper-executeasync) method. Unfortunately, `Dapper` doesn't support [cancellation token](https://github.com/DapperLib/Dapper/pull/1625#issuecomment-827984908).

In this example, we will use the same logic as the previous example with a few differences:
- Replace the `Execute` method with the `ExecuteAsync` method
- Use the `await` keyword
- The `ConfigureAwait(false)` part is optional (depending on your application type)

```csharp
// 1. We will create a connection
using (var connection = new SqlConnection(connectionString))
{
    // 2. We will create an `INSERT` sql statement
	var sql = "INSERT INTO Customers (Name, Email) VALUES (@Name, @Email)";

	// 3. Call the `ExecuteAsync` method
	{
		// 3a. The first time, we will pass parameters values with an anonymous type
		var anonymousCustomer = new { Name = "ZZZ Projects", Email = "zzzprojects@example.com" };

		var rowsAffected = await connection.ExecuteAsync(sql, anonymousCustomer);
		// or var rowsAffected = await connection.ExecuteAsync(sql, anonymousCustomer).ConfigureAwait(false);
		Console.WriteLine($"{rowsAffected} row(s) inserted.");
	}

	{
		// 3b. The second time, we will pass parameters values by providing the entity
		var customer = new Customer() { Name = "Learn Dapper", Email = "learndapper@example.com" };

		var rowsAffected = await connection.ExecuteAsync(sql, customer);
		// or var rowsAffected = await connection.ExecuteAsync(sql, customer).ConfigureAwait(false);
		Console.WriteLine($"{rowsAffected} row(s) inserted.");
	}

	var insertedCustomers = connection.Query<Customer>("SELECT * FROM Customers").ToList();
}
```

## Dapper Insert Multiple Rows

For inserting multiple rows in Dapper, you have to provide a list to the `Execute` or `ExecuteAsync` method instead of a single anonymous object or entity.

:::{.alert .alert-warning}
**NOTE:** Passing a list doesn't insert rows in bulk. Every item will be inserted by performing a single insert statement (similar to looping on your item list to call the `Execute` method)
:::

In this example:

1. We will create a connection
2. We will create an `INSERT` sql statement
3. Call the `Execute` method
   3a. The first time, we will pass parameters values with a list of anonymous objects
   3b. The second time, we will pass parameters values by providing a list of customers

```csharp
// 1. We will create a connection
using (var connection = new SqlConnection(connectionString))
{
	// 2. We will create an `INSERT` sql statement
	var sql = "INSERT INTO Customers (Name, Email) VALUES (@Name, @Email)";

	// 3. Call the `Execute` method
	{
		// 3a. The first time, we will pass parameters values with a list of anonymous objects
		var anonymousCustomers = new List<object>() {
			new { Name = "ZZZ Projects", Email = "zzzprojects@example.com" },
			new { Name = "Anonymous Object 2", Email = "anonymousobject2@example.com" },
			new { Name = "Anonymous Object 3", Email = "anonymousobject2@example.com" },
		};

		var rowsAffected = connection.Execute(sql, anonymousCustomers);
		Console.WriteLine($"{rowsAffected} row(s) inserted.");
	}

	{
		// 3b. The second time, we will pass parameters values by providing a list of customers
		var customers = new List<Customer>()
		{
			new Customer() { Name = "Learn Dapper", Email = "learndapper@example.com" },
			new Customer() { Name = "Entity 2", Email = "entity2@example.com" },
			new Customer() { Name = "Entity 3", Email = "entity3@example.com" }
		};

		var rowsAffected = connection.Execute(sql, customers);
		Console.WriteLine($"{rowsAffected} row(s) inserted.");
	}

	var insertedCustomers = connection.Query<Customer>("SELECT * FROM Customers").ToList();

```

## Dapper BulkInsert

For bulk inserting data in Dapper, the third-party library named [Dapper Plus](https://dapper-plus.net/) will be required.

You can read our tutorial on [Bulk Inserting data with Dapper](/bulk-operations/bulk-insert)

In this example using **Dapper Plus**:

1. We will create a connection
2. Call the `BulkInsert` method with a list of customers

```csharp
// 1. We will create a connection
using (var connection = new SqlConnection(connectionString))
{
	var customers = new List<Customer>()
		{
			new Customer() { Name = "Learn Dapper", Email = "learndapper@example.com" },
			new Customer() { Name = "Entity 2", Email = "entity2@example.com" },
			new Customer() { Name = "Entity 3", Email = "entity3@example.com" }
		};

	// 2. Call the `BulkInsert` method with a list of customers
	connection.BulkInsert(customers);

	var insertedCustomers = connection.Query<Customer>("SELECT * FROM Customers").ToList();
}
```

## FAQ

<div itemscope itemtype="https://schema.org/FAQPage">

<details itemscope itemprop="mainEntity" itemtype="https://schema.org/Question">
<summary id="how-to-insert-data-in-bulk-in-dapper" itemprop="name">How to insert data in bulk in Dapper?</summary>
<div itemscope itemprop="acceptedAnswer" itemtype="https://schema.org/Answer"><div itemprop="text">

To insert data in bulk, you need to use [Dapper Plus](https://dapper-plus.net/). The `BulkInsert` extension methods allow you to insert a list of entities using Bulk Operations.

Learn more about [Bulk Inserting data with Dapper](/bulk-operations/bulk-insert)

</div></div>
</details>

<details itemscope itemprop="mainEntity" itemtype="https://schema.org/Question">
<summary id="how-to-insert-data-and-return-the-id-in-dapper" itemprop="name">How to insert data and return the id in Dapper?</summary>
<div itemscope itemprop="acceptedAnswer" itemtype="https://schema.org/Answer"><div itemprop="text">

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

</div></div>
</details>

<details itemscope itemprop="mainEntity" itemtype="https://schema.org/Question">
<summary id="how-to-insert-data-including-the-identity-value-in-dapper" itemprop="name">How to insert data including the identity value in Dapper?</summary>
<div itemscope itemprop="acceptedAnswer" itemtype="https://schema.org/Answer"><div itemprop="text">

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

</div></div>
</details>

<details itemscope itemprop="mainEntity" itemtype="https://schema.org/Question">
<summary id="how-to-insert-data-using-a-list-in-dapper" itemprop="name">How to insert data using a list in Dapper?</summary>
<div itemscope itemprop="acceptedAnswer" itemtype="https://schema.org/Answer"><div itemprop="text">

To insert data using a list, you need to pass the list in a parameter instead of a single object to the `Execute` or `ExecuteAsync` method. However, inserting by providing a list doesn't perform a bulk insert. Instead, it simply iterates over every item to perform a single insert. See the [Inserting multiple rows](#dapper-insert-multiple-rows) for an example.

See the [Bulk Inserting](/bulk-operations/bulk-insert) documentation if you want to insert the list in bulk.

</div></div>
</details>

<details itemscope itemprop="mainEntity" itemtype="https://schema.org/Question">
<summary id="how-to-insert-data-using-a-stored-procedure-in-dapper" itemprop="name">How to insert data using a stored procedure in Dapper?</summary>
<div itemscope itemprop="acceptedAnswer" itemtype="https://schema.org/Answer"><div itemprop="text">

To insert data using a stored procedure, you need to call the `Execute` method with the `commandType: CommandType.StoredProcedure` parameter

Learn more about [Executing stored procedures with Dapper](/stored-procedures)
</div></div>
</details>

<details itemscope itemprop="mainEntity" itemtype="https://schema.org/Question">
<summary id="how-to-insert-data-using-a-transaction-in-dapper" itemprop="name">How to insert data using a transaction in Dapper?</summary>
<div itemscope itemprop="acceptedAnswer" itemtype="https://schema.org/Answer"><div itemprop="text">

To insert data using a transaction, you have 3 choices:

1. Begin a transaction from your connection with `connection.BeginTransaction()`
2. Create a [TransactionScope](https://learn.microsoft.com/en-us/dotnet/api/system.transactions.transactionscope)
3. Use [Dapper.Transaction](https://dapper-tutorial.net/dapper-transaction) _(**recommended solution**)_

Learn more about [Transaction with Dapper](/misc/transaction)

</div></div>
</details>

<details itemscope itemprop="mainEntity" itemtype="https://schema.org/Question">
<summary id="how-to-insert-data-using-if-not-exists-in-dapper" itemprop="name">How to insert data using if not exists in Dapper?</summary>
<div itemscope itemprop="acceptedAnswer" itemtype="https://schema.org/Answer"><div itemprop="text">

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

</div></div>
</details>

<details itemscope itemprop="mainEntity" itemtype="https://schema.org/Question">
<summary id="how-to-insert-data-using-csharp-in-dapper" itemprop="name">How to insert data using C# in Dapper?</summary>
<div itemscope itemprop="acceptedAnswer" itemtype="https://schema.org/Answer"><div itemprop="text">

Here is an example of inserting data using C# syntax:

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var sql = "INSERT INTO Customers (Name, Email) VALUES (@Name, @Email)";

	var customer = new Customer() { Name = "Learn Dapper", Email = "learndapper@example.com" };

	connection.Execute(sql, customer);
}
```

</div></div>
</details>

<details itemscope itemprop="mainEntity" itemtype="https://schema.org/Question">
<summary id="how-to-insert-data-using-vb-net-in-dapper" itemprop="name">How to insert data using VB.NET in Dapper?</summary>
<div itemscope itemprop="acceptedAnswer" itemtype="https://schema.org/Answer"><div itemprop="text">

Here is an example of inserting data using VB.NET syntax:

```vbnet
Using connection As New SqlConnection(connectionString)
    Dim sql = "INSERT INTO Customers (Name, Email) VALUES (@Name, @Email)"
    
    Dim customer = New Customer() With { .Name = "Learn Dapper", .Email = "learndapper@example.com" }
    
    connection.Execute(sql, customer)
End Using
```

</div></div>
</details>

</div>

## Conclusion

In conclusion, Dapper provides a simple and easy-to-use method for inserting data with the `Execute` and `ExecuteAsync` methods. You write your `INSERT` sql statement and provide either a single item or a list.

## Related Articles

- [Executing Non-Query Commands With Dapper](/non-query)
- [Transaction With Dapper](/misc/transaction)
- [Using Parameters With Dapper](/parameters)
- [Bulk Inserting Data With Dapper](/bulk-operations/bulk-insert)