---
PermaID: 1000180
Name: Result Multi Mapping
---

# Dapper - Result Multi-Mapping 

## Description
Extension methods can be used to execute a query and map the result to a strongly typed list with relations.

The relation can be either:
- [One to One](#example---query-multi-mapping-one-to-one)
- [One to Many](#example---query-multi-mapping-one-to-many)

These extension methods can be called from any object of type IDbConnection.
## Example - Query Multi-Mapping (One to One)
Query method can execute a query and map the result to a strongly typed list with a one to one relation.

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
Query method can execute a query and map the result to a strongly typed list with a one to many relations.

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
        splitOn: "OrderDetailID")
    .Distinct()
    .ToList();

    Console.WriteLine("Orders Count:" + list.Count);

    FiddleHelper.WriteTable(list);
    FiddleHelper.WriteTable(list.First().OrderDetails);
}
```

Try it: [.NET Core](https://dotnetfiddle.net/HClmCa) | [.NET Framework](https://dotnetfiddle.net/DPiy2b)
