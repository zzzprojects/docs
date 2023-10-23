---
PermaID: 1000197
Title: Dapper Contrib Update - Learn How to Update Data From a Table
MetaDescription: Unlock the power of Dapper Contrib with the Update method to update data from a table. Learn how to use the simplest way to update rows from a database table without writing any SQL.
LastMod: 2023-10-21
---

# Dapper Contrib Update: Discover How to Update Data From a Table

## Description

When updating information in a database, you need to specify the name of the table and the name and value of each column in the SQL statement. The `Dapper.Contrib` library provides the `Update` method which is a pretty simple way of updating the data. 

 - With the `Update` method, we don't need to write any SQL statements, we just need to pass in the object that needs to be updated as a parameter to the `Update` method.
 - The `Update` method updates a given entity in the database. 

### Parameters

The syntax of the `Update<T>` method is given below:

```csharp
public static bool Update<T>(T entityToUpdate, IDbTransaction transaction = null, int? commandTimeout = null)
```

The following table shows the different parameters of an `Update` method.

| Name | Description |
| :--- | :---------- |
| entityToUpdate | Entity to update, can be a list of entities. |
| transaction    | It represents a database transaction. This parameter is optional if we are using this method outside of a transaction, otherwise, it is required. |
| commandTimeout | It represents the time in seconds to wait before terminating the command execution and generating an error. The default value of this parameter is 30 seconds. This parameter is optional. |

The `Update` method returns `true` if updated, and `false` if not found or not modified. With the `Update` method, you can update a single or many entities.

- [Update Single](#example---update-single)
- [Update Many](#example---update-single)

Dapper.Contrib's `Update` method is used to update an existing record in the database. The `Update` method takes an object as a parameter and updates the records in the database based on the property values of the object.

## Example - Update Single

When you need to update a single record, you need to pass the object directly to the `Update` method that needs to be updated as shown in the below example.

```csharp
using (var connection = My.ConnectionFactory())
{
    connection.Open();

    var isSuccess = connection.Update(new Invoice { InvoiceID = 1, Code = "Update_Single_1"});
}
```

## Example - Update Many

If you need to update multiple records in the database then you can use the `Update` method and pass a list of objects that needs to be updated.

The following example shows how to update multiple objects by passing the list as an argument to the `Update` method.

```csharp
using (var connection = My.ConnectionFactory())
{
    connection.Open();

    var list = new List<Invoice>()
    {
        new Invoice {InvoiceID = 1, Code = "Update_Many_1"},
        new Invoice {InvoiceID = 2, Code = "Update_Many_2"},
        new Invoice {InvoiceID = 3, Code = "Update_Many_3"}
    };

    var isSuccess = connection.Update(list);
}
```

## Related Articles

- [Dapper Update](/execute#example-execute-update)
- [Dapper Plus Bulk Update](/bulk-update)