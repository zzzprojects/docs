---
PermaID: 1000171
Title: Dapper List Parameter - Learn How to Execute Where 'IN' Clause
MetaDescription: Unlock the power of Dapper with List Parameter to execute `where in` clause. Learn how to use a list or an array that will automatically be converted into a SQL `IN` clause.
LastMod: 2023-10-20
---

# Dapper List Parameter: Discover How to Execute Where 'IN' Clause

## Description

Dapper supports multiple ways to specify list parameters. The simplest is just passing comma-separated values. Dapper allows you to specify multiple parameters on an `IN` clause by using a list.

 - The list parameters are passed as an anonymous type which means that the definition of the object doesn't matter. 
 - The object is anonymous, you need to give each parameter a unique name. 
 - This can be done by using the syntax param1, param2, etc., or by giving each property in the object a different name.

For example, let's say we want to pass a list of `IDs` to a SQL query and we would use that SQL statement in the [Query](/query) method as shown below. 

```csharp
var ids = new[] { 1, 2, 3 };

var sql = "SELECT * FROM Customers WHERE CustomerID IN @Ids;";

using (var connection = My.ConnectionFactory())
{
	connection.Open();

	var customer = connection.Query<Customer>(sql, new {Ids = ids }).ToList();
}
```

Notice that we have given the `Ids` parameter a unique name. It will return all customers that match the above query.

In Dapper, the `IN` clause is used to specify a list of values to be compared against a column in a `WHERE` clause. The SQL `IN` predicate can be used to easily test if an expression matches any value in a list. 

For example, let's say we have different kinds of invoices and we want to find all records in our database table that have a value matching either `StoreInvoice` or `WebInvoice`. We can use the `IN` clause to do this as shown in the below example.

```csharp
var sql = "SELECT * FROM Invoice WHERE Kind IN @Kind;";

using (var connection = My.ConnectionFactory())
{
	connection.Open();

	var invoices = connection.Query<Invoice>(sql, new {Kind = new[] {InvoiceKind.StoreInvoice, InvoiceKind.WebInvoice}}).ToList();
}

```

In the above example, we are using the `@Ids` and `@Kind` parameters to pass in our list of values. Dapper will automatically convert this into a SQL `IN` clause.

You can also use the `Execute` method to execute a SQL query that contains an `IN` clause. This is useful when you want to delete or update multiple records at once. For example, suppose we want to delete a list of customers as shown in the below example.

```csharp
var ids = new[] { 2, 3, 5 };

var sql = "DELETE FROM Customers WHERE CustomerID IN @Ids";

using (var connection = My.ConnectionFactory())
{
	connection.Open();

	connection.Execute(sql, new { Ids = ids });
}
```

As you can see, using Dapper's `IN` clause support is a simple and convenient way to work with lists of values in your SQL queries.

## Related Articles

- [Dapper Query](/query)