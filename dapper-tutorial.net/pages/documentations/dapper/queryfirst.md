---
PermaID: 1000174
Name: QueryFirst
IsUpdated: 1
---

# Dapper - QueryFirst 

## Description
QueryFirst method is an extension method that can be called from any object of type IDbConnection. It can execute a query and map the first result.

The result can be mapped to:

- [Anonymous](#example---query-anonymous)
- [Strongly Typed](#example---query-strongly-typed)

### Parameters
The following table shows the different parameters of a QueryFirst method.

| Name | Description |
| :--- | :---------- |
| sql            | The query to execute. |
| param          | The query parameters (default = null). |
| transaction    | The transaction to use (default = null). |
| commandTimeout | The command timeout (default = null) |
| commandType    | The command type (default = null) |

### First, Single & Default
Be careful to use the right method. First & Single methods are very different.

| Result          | No Item   | One Item | Many Items |
| :-------------- | :-------: | :------: | :--------: |
| First           | Exception | Item     | First Item |
| Single          | Exception | Item     | Exception  |
| FirstOrDefault  | Default   | Item     | First Item |
| SingleOrDefault | Default   | Item     | Exception  |

## Example - Query Anonymous
Execute a query and map the first result to a dynamic list.

```csharp
string sql = "SELECT * FROM OrderDetails WHERE OrderDetailID = @OrderDetailID;";

using (var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServerW3Schools()))
{
    var orderDetail = connection.QueryFirst(sql, new {OrderDetailID = 1});

    FiddleHelper.WriteTable(orderDetail);
}
```

Try it: [.NET Core](https://dotnetfiddle.net/CDQKMN) | [.NET Framework](https://dotnetfiddle.net/eogWc1)

## Example - Query Strongly Typed
Execute a query and map the first result to a strongly typed list.

```csharp
string sql = "SELECT * FROM OrderDetails WHERE OrderDetailID = @OrderDetailID;";

using (var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServerW3Schools()))
{
    var orderDetail = connection.QueryFirst<OrderDetail>(sql, new {OrderDetailID = 1});

    FiddleHelper.WriteTable(orderDetail);
}
```

Try it: [.NET Core](https://dotnetfiddle.net/FTB4ih) | [.NET Framework](https://dotnetfiddle.net/AV0OgZ)
