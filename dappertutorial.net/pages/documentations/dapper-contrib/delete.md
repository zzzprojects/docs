---
PermaID: 1000192
Title: Dapper Contrib Delete - Learn How to Delete Data From a Table
MetaDescription: Unlock the power of Dapper Contrib with the Delete method to delete data from a table. Learn how to use the simplest way to delete rows from a database table without writing any SQL.
LastMod: 2023-10-21
---

# Dapper Contrib Delete: Discover How to Delete Data From a Table

## Description

Database Delete operation refers to the process of deleting data from a database table. To delete data from a database, the user must have the `DELETE` privilege on the table. The `DELETE` statement is used to delete records from a database table.

The `Dapper.Contrib` library provides the `Delete` method which is a pretty simple way of deleting the data. 

 - With the `Delete` method, we don't need to write any SQL statements, we just need to pass in the object that needs to be deleted as a parameter to the `Delete` method.
 - The `Delete` method deletes a given entity in the database. 

### Parameters

The syntax of the `Delete<T>` method is given below:

```csharp
public static bool Delete<T>(T entityToDelete, IDbTransaction transaction = null, int? commandTimeout = null)
```

The following table shows the different parameters of a `Delete` method.

| Name | Description |
| :--- | :---------- |
| entityToDelete | Entity to delete, can be a list of entities. |
| transaction    | It represents a database transaction. This parameter is optional if we are using this method outside of a transaction, otherwise, it is required. |
| commandTimeout | It represents the time in seconds to wait before terminating the command execution and generating an error. The default value of this parameter is 30 seconds. This parameter is optional. |

The `Delete` method returns `true` if deleted, `false` if not found. With the `Delete` method, you can delete a single or many entities.

- [Delete Single](#example---delete-single)
- [Delete Many](#example---delete-single)

## Example - Delete Single

To delete an entity, simply call the `Delete` method and pass in your entity. The following example deletes a single entity using the `Delete` method.

```csharp
using (var connection = My.ConnectionFactory())
{
    connection.Open();

    var isSuccess = connection.Delete(new Invoice {InvoiceID = 1});
}
```

## Example - Delete Many

If you would like to delete multiple entities at once, you can pass in an `IEnumerable<T>` to the `Delete` method as shown in the following example.

```csharp
using (var connection = My.ConnectionFactory())
{
    connection.Open();

    var list = new List<Invoice>()
    {
        new Invoice {InvoiceID = 1},
        new Invoice {InvoiceID = 2},
        new Invoice {InvoiceID = 3}
    };

    var isSuccess = connection.Delete(list);
}
```

## Related Articles

- [Dapper Delete](/execute#example-execute-delete)
- [Dapper Plus Bulk Delete](/bulk-delete)