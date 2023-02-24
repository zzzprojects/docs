---
PermaID: 1000195
Name: GetAll in Dapper Contrib
LastMod: 2023-02-25
---

# Dapper Contrib - GetAll

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