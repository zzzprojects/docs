---
PermaID: 1000193
Title: Dapper Contrib DeleteAll - Learn How to Delete All Data From a Table
MetaDescription: Unlock the power of Dapper Contrib with the DeleteAll method to delete all data from a table. Learn how to use the simplest way to delete all rows from a database table without writing any SQL.
LastMod: 2023-10-21
---

# Dapper Contrib DeleteAll: Discover How to Delete All Data From a Table

## Description

The `Dapper.Contrib` library also provides the `DeleteAll<T>()` extension method that allows you to delete all records from a database table.

### Parameters

The syntax of the `DeleteAll<T>` method is given below:

```csharp
public static bool DeleteAll<T>(IDbTransaction transaction = null, int? commandTimeout = null)
```

The following table shows the different parameters of a `DeleteAll` method.

| Name | Description |
| :--- | :---------- |
| transaction    | It represents a database transaction. This parameter is optional if we are using this method outside of a transaction, otherwise, it is required. |
| commandTimeout | It represents the time in seconds to wait before terminating the command execution and generating an error. The default value of this parameter is 30 seconds. This parameter is optional. |

In the following example, we will use the `DeleteAll` method to delete all the records from the database table.

```csharp
using (var connection = My.ConnectionFactory())
{
    connection.Open();

    var isSuccess = connection.DeleteAll<Invoice>();
}
```

## Related Articles

- [Dapper Delete](/execute#example-execute-delete)
- [Dapper Plus Bulk Delete](/bulk-delete)