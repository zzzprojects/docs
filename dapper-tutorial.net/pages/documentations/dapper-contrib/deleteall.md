---
PermaID: 1000193
Name: DeleteAll in Dapper Contrib
LastMod: 2023-02-25
---

# Dapper Contrib - DeleteAll

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