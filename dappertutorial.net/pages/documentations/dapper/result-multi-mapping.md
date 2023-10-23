---
PermaID: 1000180
Title: Dapper Multi-Mapping Result - Learn How to Map Result to Many Types
MetaDescription: Unlock the power of Dapper by returning a Multi-Mapping Result to map multiple entity types or DTOs in one query. Learn how to query your database and map your navigation by mapping many types.
LastMod: 2023-10-20
---

# Dapper Multi-Mapping Result: Discover How to Map Result to Many Types

## Description

Multi-mapping is the process of mapping multiple objects to a single object. This is usually done when there are multiple objects that need to be mapped to a single object. 

 - Dapper supports multi-mapping, which allows you to map a single row to multiple objects. 
 - This is useful if you need to retrieve data from multiple tables in a single query. 
 - To do this, you need to use the `SplitOn` parameter when calling the `Query` method. 
 - The `SplitOn` parameter tells Dapper what column(s) to use to split the data into multiple objects. 

The Dapper `Query` method allows you to execute a query and map the result to a strongly typed list with relations.

The relation can be either:
- [One to One](#example---query-multi-mapping-one-to-one)
- [One to Many](#example---query-multi-mapping-one-to-many)

The `Query` method can be called from any object of type `IDbConnection`.

## Example - Query Multi-Mapping (One-to-One)

A one-to-one relationship is a relationship between two entities where each entity can only be associated with one other entity. 

 - For example, a one-to-one relationship could exist between an `Invoice` and an `InvoiceDetail`. 
 - In this relationship, each `Invoice` can only have one `InvoiceDetail`, and each `InvoiceDetail` can only be associated with one `Invoice`.

With the Dapper multi-mapping feature, you can easily configure the one-to-one relationship. The raw SQL query can be executed using the `Query` method and map the result to a strongly typed list with a one-to-one relation.

Now let's say we want to fetch all the invoices, and also get invoice detail for each invoice. We can do this with the following code.

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
In the above example, the input parameters are `Invoice` and an `InvoiceDetail`, and the return type is `Invoice` (`<Invoice, InvoiceDetail, Invoice>`). So we are telling the `Query` method to take an `invoice` and `invoiceDetail`, process them in some way to be defined, and return an `invoice`.

The `splitOn` argument tells Dapper to split the data on the `InvoiceID` column. Anything up to that column maps to the first parameter `Invoice`, and anything else from that column onward should be mapped to the second input parameter `InvoiceDetail`.

## Example - Query Multi-Mapping (One to Many)

In a one-to-many relationship, each record in the first table can be linked to zero, one, or multiple records in the second table. For example, an order can have many orderDetail. In this case, the `OrderID` field in the `OrderDetails` table is said to be a foreign key that references the `Orders` table.

The Dapper multi-mapping feature allows you to configure the one-to-many relationship easily. The raw SQL query can be executed using the `Query` method and map the result to a strongly typed list with one-to-many relations.

To map these results to `Order` and an `OrderDetail` objects, multi-map and split on the `OrderID` column. The map function is more complex in the one-to-many scenario.

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
In the above example, the input parameters are `Order` and an `OrderDetail`, and the return type is `Order` (`<Order, OrderDetail, Order>`). So we are telling the `Query` method to take an `order` and `orderDetail`, process them in some way to be defined, and return an `order`. It means, first map the columns to an `Order` object, then to an `OrderDetail` object, and return `IEnumerable<Order>`.

Try it: [.NET Core](https://dotnetfiddle.net/HClmCa) | [.NET Framework](https://dotnetfiddle.net/DPiy2b)
