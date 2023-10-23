---
PermaID: 1000172
Title: Dapper String Parameter - Learn How to Improve Mapping With DbString
MetaDescription: Unlock the power of Dapper with String Parameter to improve your mapping with 'DbString'. Learn how to pass an explicit string to improve type mapping, database compatibility, and performance.
LastMod: 2023-10-20
---

# Dapper String Parameter: Discover How to Improve Mapping With DbString

## Description

The Dapper library provides different extension methods that make it easy to execute CRUD operations against a relational database. 

 - These extension methods are very convenient, but it does have one potential downside. 
 - If you're not careful, it's easy to introduce SQL injection vulnerabilities into your application when using these methods.

The Dapper library provides several ways to help prevent SQL injection attacks, including parameterized queries and the use of the `DbString` class. 

### DbString Class

To help prevent SQL injection attacks, Dapper provides a special `DbString` class that can be used to parameterize queries.

 - The `DbString` class allows you to specify the type of data (such as `varchar`, `nvarchar`, etc), the length of the data, and whether the data is nullable. 
 - This allows the library to properly escape any potentially dangerous characters in the data before sending it to the database server.
 - This ensures that any user input is properly escaped before being used in a query.

Let's take a look at how to use the `DbString` class to safely execute SQL queries with Dapper. When executing a SQL query with Dapper, you simply need to create a new instance of the `DbString` class and pass it in as a parameter to the Query or Execute methods.

The following example shows how to use the `DbString` class to safely query for an invoice code:
```csharp
var sql = "SELECT * FROM Invoice WHERE Code = @Code;";

using (var connection = My.ConnectionFactory())
{
	connection.Open();

	var invoices = connection.Query<Invoice>(sql, new {Code = new DbString {Value = "Invoice_1", IsFixedLength = false, Length = 9, IsAnsi = true}}).ToList();

	My.Result.Show(invoices);
}
```

In addition to using the DbString class, you should also always use parameterized queries when working with databases. 