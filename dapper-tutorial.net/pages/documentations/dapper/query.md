---
PermaID: 1000173
Name: Query
---

# Dapper - Query 

## Description
Query method is an extension method that can be called from any object of type IDbConnection. It can execute a query and map the result.

The result can be mapped to:

- [Anonymous](#example---query-anonymous)
- [Strongly Typed](#example---query-strongly-typed)
- [Multi-Mapping (One to One)](#example---query-multi-mapping-one-to-one)
- [Multi-Mapping (One to Many)](#example---query-multi-mapping-one-to-many)
- [Multi-Type](#example---query-multi-type)

### Parameters
The following table shows the different parameters of a Query method.

| Name | Description |
| :--- | :---------- |
| sql         | The query to execute. |
| param       | The query parameters (default = null). |
| transaction | The transaction to use (default = null). |
| buffered    | True to buffer readeing the results of the query (default = true). |
| commandTimeout | The command timeout (default = null) |
| commandType    | The command type (default = null) |

## Example - Query Anonymous
The raw SQL query can be executed using the Query method and map the result to a dynamic list.

```csharp
string sql = "SELECT TOP 10 * FROM OrderDetails";

using (var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServerW3Schools()))
{    
    var orderDetail = connection.Query(sql).FirstOrDefault();

    FiddleHelper.WriteTable(orderDetail);
}
```

Try it: [.NET Core](https://dotnetfiddle.net/5GFAdJ) | [.NET Framework](https://dotnetfiddle.net/1K2DU4)

## Example - Query Strongly Typed
The raw SQL query can be executed using the Query method and map the result to a strongly typed list.

```csharp
string sql = "SELECT TOP 10 * FROM OrderDetails";

using (var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServerW3Schools()))
{            
    var orderDetails = connection.Query<OrderDetail>(sql).ToList();

    Console.WriteLine(orderDetails.Count);

    FiddleHelper.WriteTable(orderDetails);
}
```

Try it: [.NET Core](https://dotnetfiddle.net/CvMkj8) | [.NET Framework](https://dotnetfiddle.net/dXZc0s)

## Example - Query Multi-Mapping (One to One)
The raw SQL query can be executed using the Query method and map the result to a strongly typed list with a one to one relation.

```csharp
string sql = "SELECT * FROM Invoice AS A INNER JOIN InvoiceDetail AS B ON A.InvoiceID = B.InvoiceID;";

using (var connection = My.ConnectionFactory())
{
    connection.Open();

    var invoices = connection.Query<Invoice, InvoiceDetail, Invoice>(
            sql,
            (invoice, invoiceDetail) =>
            {
                invoice.InvoiceDetail = invoiceDetail;
                return invoice;
            },
            splitOn: "InvoiceID")
        .Distinct()
        .ToList();
}
```

## Example - Query Multi-Mapping (One to Many)
The raw SQL query can be executed using the Query method and map the result to a strongly typed list with one-to-many relations.

```csharp
string sql = "SELECT TOP 10 * FROM Orders AS A INNER JOIN OrderDetails AS B ON A.OrderID = B.OrderID;";

using (var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServerW3Schools()))
{            
    var orderDictionary = new Dictionary<int, Order>();


    var list = connection.Query<Order, OrderDetail, Order>(
    sql,
    (order, orderDetail) =>
    {
        Order orderEntry;

        if (!orderDictionary.TryGetValue(order.OrderID, out orderEntry))
        {
        orderEntry = order;
        orderEntry.OrderDetails = new List<OrderDetail>();
        orderDictionary.Add(orderEntry.OrderID, orderEntry);
        }

        orderEntry.OrderDetails.Add(orderDetail);
        return orderEntry;
    },
    splitOn: "OrderID")
    .Distinct()
    .ToList();

    Console.WriteLine(list.Count);

    FiddleHelper.WriteTable(list);
    FiddleHelper.WriteTable(list.First().OrderDetails);
}
```

Try it: [.NET Core](https://dotnetfiddle.net/HClmCa) | [.NET Framework](https://dotnetfiddle.net/DPiy2b)

## Example - Query Multi-Type
The raw SQL query can be executed using the Query method and map the result to a list of different types.

```csharp
string sql = "SELECT * FROM Invoice;";

using (var connection = My.ConnectionFactory())
{
    connection.Open();

    var invoices = new List<Invoice>();

    using (var reader = connection.ExecuteReader(sql))
    {
        var storeInvoiceParser = reader.GetRowParser<StoreInvoice>();
        var webInvoiceParser = reader.GetRowParser<WebInvoice>();

        while (reader.Read())
        {
            Invoice invoice;

            switch ((InvoiceKind) reader.GetInt32(reader.GetOrdinal("Kind")))
            {
                case InvoiceKind.StoreInvoice:
                    invoice = storeInvoiceParser(reader);
                    break;
                case InvoiceKind.WebInvoice:
                    invoice = webInvoiceParser(reader);
                    break;
                default:
                    throw new Exception(ExceptionMessage.GeneralException);
            }

            invoices.Add(invoice);
        }
    }
    
    My.Result.Show(invoices);
}
```
