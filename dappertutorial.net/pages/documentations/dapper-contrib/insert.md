---
PermaID: 1000196
Title: Dapper Contrib Insert - Learn How to Insert Data in a Table
MetaDescription: Unlock the power of Dapper Contrib with the Insert method to insert data in a table. Learn how to use the simplest way to insert rows in a database table without writing any SQL.
LastMod: 2023-10-21
---

# Dapper Contrib Insert: Discover How to Insert Data in a Table

## Description

When inserting information into a database, you need to specify the name of the table and the name and value of each column in the SQL statement. The `Dapper.Contrib` library provides the `Insert` method which is a pretty simple way of inserting the data. 

 - With the `Insert` method, we don't need to write any SQL statements, we just need to pass in the object that needs to be inserted as a parameter to the `Insert` method.
 - The `Insert` method inserts a given entity into the database. 

### Parameters

The syntax of the `Insert<T>` method is given below:

```csharp
public static long Insert<T>(T entityToInsert, IDbTransaction transaction = null, int? commandTimeout = null)
```
The following table shows the different parameters of an `Insert` method.

| Name | Description |
| :--- | :---------- |
| entityToInsert | Entity to insert, can be a list of entities. |
| transaction    | It represents a database transaction. This parameter is optional if we are using this method outside of a transaction, otherwise, it is required. |
| commandTimeout | It represents the time in seconds to wait before terminating the command execution and generating an error. The default value of this parameter is 30 seconds. This parameter is optional. |

The `Insert` method returns the identity of the inserted entity, or a number of inserted rows if inserting a list. With the `Insert` method, you can insert a single or many entities.

- [Insert Single](#example---insert-single)
- [Insert Many](#example---insert-single)

## Example - Insert Single

When you need to insert a single record, you can create an object of your entity and pass it directly to the `Insert` method as shown in the below example.

```csharp
using (var connection = My.ConnectionFactory())
{
    connection.Open();

    var identity = connection.Insert(new InvoiceContrib {Kind = InvoiceKind.WebInvoice, Code = "Insert_Single_1"});
}
```

## Example - Insert Many

If you need to insert multiple records into the database then you can use the `Insert` method and pass a list of objects. The method will then insert each object in the list into the database.

The following example creates a list of three objects and passes the list as an argument to the `Insert` method.

```csharp
using (var connection = My.ConnectionFactory())
{
    connection.Open();

    var list = new List<InvoiceContrib>
    {
        new InvoiceContrib {Kind = InvoiceKind.WebInvoice, Code = "Insert_Many_1"},
        new InvoiceContrib {Kind = InvoiceKind.WebInvoice, Code = "Insert_Many_2"},
        new InvoiceContrib {Kind = InvoiceKind.StoreInvoice, Code = "Insert_Many_3"}
    };

    var identity = connection.Insert(list);
}
```

It is important to note that the `Insert` method does not support inserting multiple objects into a table with a composite primary key. If you need to insert multiple objects into a table with a composite primary key, you will need to use the Execute method.

## Related Articles

- [Dapper Insert](/execute#example-execute-insert)
- [Dapper Plus Bulk Insert](/bulk-insert)