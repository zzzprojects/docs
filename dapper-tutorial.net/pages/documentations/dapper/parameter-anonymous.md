---
PermaID: 1000169
Name: Parameter Anonymous
---

# Dapper - Parameter Anonymous 

## Description
Dapper make it simple & safe (SQL Injection) to use parameter by supporting anonymous type.

### Single
Execute a single time a SQL Command.

```csharp
string sql = "INSERT INTO Customers (CustomerName) Values (@CustomerName);";

using (var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServerW3Schools()))
{
	var affectedRows = connection.Execute(sql, new {CustomerName = "Mark"});

	Console.WriteLine(affectedRows);
	
	// Only for see the Insert.
	var customer = connection.Query<Customer>("Select * FROM CUSTOMERS WHERE CustomerName = 'Mark'").ToList();

	FiddleHelper.WriteTable(customer);
}
```
Try it: [.NET Core](https://dotnetfiddle.net/wNl0G3) | [.NET Framework](https://dotnetfiddle.net/Z1iRIQ)

### Many
Execute many times a SQL Command

```csharp
string sql = "INSERT INTO Customers (CustomerName) Values (@CustomerName);";

using (var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServerW3Schools()))
{			
	var affectedRows = connection.Execute(sql,
	new[]
	{
	new {CustomerName = "John"},
	new {CustomerName = "Andy"},
	new {CustomerName = "Allan"}
	}
	
	Console.WriteLine(affectedRows);
)
```
Try it: [.NET Core](https://dotnetfiddle.net/df2ZDH) | [.NET Framework](https://dotnetfiddle.net/fvRKsY)
