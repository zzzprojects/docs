---
PermaID: 1000173
Title: Dapper Query - Learn How to Query Your Database and Return a List
MetaDescription: Unlock the power of Dapper Query to optimize your C# database operations. Learn how to use Query and QueryAsync to return a list of anonymous type or strongly typed entities.
LastMod: 2023-10-20
---

# Dapper Query: Discover How to Query Your Database and Return a List

## Description

The `Query` extension method can be called from any object of type `IDbConnection`. It can execute a query and map the result. One of its key features is the ability to map query results to strongly typed objects. This allows for easy and convenient access to data without the need for manual mapping.

 - The Query method executes a SQL query and returns the results as a list of dynamic objects. 
 - The returned object is of type `dynamic`, which means that you can access its properties using dynamic syntax.
 - One of the drawbacks of using an ORM is that they can often be slow and cumbersome. 
 - This is because they typically use reflection to map objects to database columns. 
 - Dapper avoids this overhead by using dynamic typecasting to map column values to object properties. 
 - This makes it one of the fastest ORMs available.

The result can be mapped to:

- [Anonymous](#example---query-anonymous)
- [Strongly Typed](#example---query-strongly-typed)
- [Multi-Mapping (One to One)](#example---query-multi-mapping-one-to-one)
- [Multi-Mapping (One to Many)](#example---query-multi-mapping-one-to-many)

### Parameters

The syntax of the `Query<T>` method is given below:

```csharp
IEnumerable<T> Query<T>(string sql, 
           object param = null, 
           IDbTransaction transaction = null, 
           bool buffered = true, 
           int? commandTimeout = null, 
           CommandType? commandType = null);
```

The `Query` method takes a SQL query as a string and an optional object parameter. The object parameter can be used to pass parameters to the SQL query.

The `Query` method also takes optional parameters for transaction, command timeout, and command type.

The following table shows the different parameters of a `Query` method.

| Name | Description |
| :--- | :---------- |
| sql            | It represents an SQL query or stored procedure. This parameter is required. |
| param          | It represents the parameters required by SQL query or stored procedure. This parameter is optional. We can pass the parameter to SQL in anonymous type, `dynamic` object, or `Dapper.DynamicParameters` class. |
| transaction    | It represents a database transaction. This parameter is optional if we are using this method outside of a transaction, otherwise, it is required. |
| buffered       | It specifies whether to execute and buffer the results of the query in memory before returning. The default value of this parameter is true. This parameter is optional.|
| commandTimeout | It represents the time in seconds to wait before terminating the command execution and generating an error. The default value of this parameter is 30 seconds. This parameter is optional. |
| commandType    | It specifies how SQL query or stored procedure should be interpreted by the data provider. The default value of this parameter is Text. This parameter is optional. |

## Example - Query Anonymous

The raw SQL query can be executed using the `Query` method and map the result to a dynamic list. These are useful for simple queries where you don't need to create a separate class to represent your data. This can save you a considerable amount of time and effort when working with complex SQL queries.

You could store the results of this query in a [Dapper anonymous result type](/result-anonymous) as follows:

```csharp
string sql = "SELECT TOP 10 * FROM OrderDetails";

using (var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServerW3Schools()))
{    
    var orderDetail = connection.Query(sql).FirstOrDefault();

    FiddleHelper.WriteTable(orderDetail);
}
```

In this example, the variable `orderDetail` would be of type `dynamic`. This means that you can access the columns of the result set by their names.

Try it: [.NET Core](https://dotnetfiddle.net/5GFAdJ) | [.NET Framework](https://dotnetfiddle.net/1K2DU4)

## Example - Query Strongly Typed

Dapper provides you with the ability to get your [query results as strongly typed objects](/result-strongly-typed) instead of just getting back a dynamic object. The raw SQL query can be executed using the `Query` method and map the result to a strongly typed list.

First, let's create a simple class that we can use to map our query results and we will call this class `OrderDetail`.

```csharp
public class OrderDetail
{
	public int OrderDetailID { get; set; }
	public int OrderID { get; set; }
	public int ProductID { get; set; }
	public int Quantity { get; set; }
}
```

Next, we will need to write our SQL query. For this example, we will just be querying for all OrderDetails in our database.

```csharp
string sql = "SELECT TOP 10 * FROM OrderDetails";

using (var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServerW3Schools()))
{            
    var orderDetails = connection.Query<OrderDetail>(sql).ToList();

    Console.WriteLine(orderDetails.Count);

    FiddleHelper.WriteTable(orderDetails);
}
```

In the above, you can see that Dapper makes it very easy to map your query results to strongly typed objects. 

Try it: [.NET Core](https://dotnetfiddle.net/CvMkj8) | [.NET Framework](https://dotnetfiddle.net/dXZc0s)

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
    splitOn: "OrderID")
    .Distinct()
    .ToList();

    Console.WriteLine(list.Count);

    FiddleHelper.WriteTable(list);
    FiddleHelper.WriteTable(list.First().OrderDetails);
}
```
In the above example, the input parameters are `Order` and an `OrderDetail`, and the return type is `Order` (`<Order, OrderDetail, Order>`). So we are telling the `Query` method to take an `order` and `orderDetail`, process them in some way to be defined, and return an `order`. It means, first map the columns to an `Order` object, then to an `OrderDetail` object, and return `IEnumerable<Order>`.

Try it: [.NET Core](https://dotnetfiddle.net/HClmCa) | [.NET Framework](https://dotnetfiddle.net/DPiy2b)

## Related Articles

- [Dapper - Anonymous Result](/result-anonymous) 
- [Dapper - Strongly Typed Result](/result-strongly-typed)
