---
PermaID: 1000194
Title: Dapper Contrib Get - Learn How to Retrieve Data From a Table
MetaDescription: Unlock the power of Dapper Contrib with the Get method to retrieve data from a table. Learn how to use the simplest way to get rows from a database table without writing any SQL.
LastMod: 2023-10-21
---

# Dapper Contrib Get: Discover How to Retrieve Data From a Table

## Description

To retrieve the desired data the user presents a set of criteria through a query. In SQL, to retrieve data stored in our tables, we use the `SELECT` statement.

The `Dapper.Contrib` library provides a `Get<T>(id)` extension method to retrieve a single record from the database based on the `id` passed as an argument and populate the data in your object model.

 - The `Get` method is used to fetch a single record from the database. 
 - The method takes an id as a parameter and returns the matching record from the database.
 - If no matching record is found, then the method returns `null`.

### Parameters

The syntax of the `Get<T>` method is given below:

```csharp
public static T Get<T>(dynamic id, IDbTransaction transaction = null, int? commandTimeout = null)
```

The following table shows the different parameters of a `Get` method.

| Name | Description |
| :--- | :---------- |
| id             | Id of the entity to get must be marked with [Key] attribute. This parameter is required. |
| transaction    | It represents a database transaction. This parameter is optional if we are using this method outside of a transaction, otherwise, it is required. |
| commandTimeout | It represents the time in seconds to wait before terminating the command execution and generating an error. The default value of this parameter is 30 seconds. This parameter is optional. |

The following example retrieves a record from the database where the `id` is 1.

```csharp
using (var connection = My.ConnectionFactory())
{
    connection.Open();

    var invoice = connection.Get<Invoice>(1);
}
```

It will return a single entity by a single id from the table. 