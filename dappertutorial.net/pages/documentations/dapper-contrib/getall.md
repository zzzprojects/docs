---
PermaID: 1000195
Title: Dapper Contrib GetAll - Learn How to Retrieve All Data From a Table
MetaDescription: Unlock the power of Dapper Contrib with the GetAll method to retrieve all data from a table. Learn how to use the simplest way to get all rows from a database table without writing any SQL.
LastMod: 2023-10-21
---

# Dapper Contrib GetAll: Discover How to Retrieve All Data From a Table

## Description

The `Dapper.Contrib` library also provides the `GetAll<T>()` extension method that allows you to retrieve all the data from the database and populate data in your object model.

### Parameters

The syntax of the `GetAll<T>` method is given below:

```csharp
public static IEnumerable<T> GetAll<T>(IDbTransaction transaction = null, int? commandTimeout = null)
```

The following table shows the different parameters of a `GetAll` method.

| Name | Description |
| :--- | :---------- |
| transaction    | It represents a database transaction. This parameter is optional if we are using this method outside of a transaction, otherwise, it is required. |
| commandTimeout | It represents the time in seconds to wait before terminating the command execution and generating an error. The default value of this parameter is 30 seconds. This parameter is optional. |

In the following example, we will use the `GetAll` method to fetch all the records from the database table.

```csharp
using (var connection = My.ConnectionFactory())
{
    connection.Open();

    var invoices = connection.GetAll<Invoice>().ToList();
}
```