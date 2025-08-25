---
Title: Dapper QueryUnbufferedAsync - Learn How to Stream Query Results Asynchronously
MetaDescription: Unlock the power of Dapper QueryUnbufferedAsync to handle large datasets efficiently. Learn how to stream results asynchronously to anonymous or strongly typed entities without loading everything into memory.
LastMod: 2025-08-25
---

# Dapper QueryUnbufferedAsync: Stream Query Results Asynchronously

## Description

The `QueryUnbufferedAsync` extension method can be called from any object of type `IDbConnection`.  
It works like `QueryAsync`, but instead of buffering all rows into memory first, it streams them one by one.  

This makes it ideal when:

- You are dealing with **large result sets** that do not fit comfortably in memory.  
- You want to **start processing rows immediately** while the rest are still being fetched.  
- You prefer to **keep memory usage low** by not storing the entire result at once.  

Dapper still provides the same fast object mapping, but combined with async streaming for optimal performance.

The result can be streamed to:

- [Anonymous](#example-queryunbufferedasync-anonymous)
- [Strongly Typed](#example-queryunbufferedasync-strongly-typed)
- [Multi-Mapping (One to One)](#example-queryunbufferedasync-multi-mapping-one-to-one)
- [Multi-Mapping (One to Many)](#example-queryunbufferedasync-multi-mapping-one-to-many)

### Parameters

The syntax of the `QueryUnbufferedAsync<T>` method is given below:

```csharp
Task<IEnumerable<T>> QueryUnbufferedAsync<T>(string sql,
           object param = null,
           IDbTransaction transaction = null,
           int? commandTimeout = null,
           CommandType? commandType = null,
           CancellationToken cancellationToken = default);
````

The method takes a SQL query and optional parameters. It behaves almost the same as `QueryAsync`, except that the results are **not buffered**. You should enumerate them with `await foreach` in C# 8 or by materializing them as needed.

| Name              | Description                                                                                              |
| :---------------- | :------------------------------------------------------------------------------------------------------- |
| sql               | The SQL query or stored procedure. Required.                                                             |
| param             | Parameters for the SQL query. Can be anonymous type, `dynamic`, or `Dapper.DynamicParameters`. Optional. |
| transaction       | A database transaction. Optional unless you need transactional consistency.                              |
| commandTimeout    | Time (in seconds) before the command execution is canceled. Default is 30 seconds. Optional.             |
| commandType       | How the command should be interpreted (Text or StoredProcedure). Default is Text. Optional.              |
| cancellationToken | Token to cancel the operation. Optional.                                                                 |

---

## Example - QueryUnbufferedAsync Anonymous

You can execute raw SQL queries and stream results as anonymous objects. Useful when you don’t need a strongly typed class.

```csharp
string sql = "SELECT TOP 10 * FROM OrderDetails";

using (var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServerW3Schools()))
{
    await foreach (var orderDetail in connection.QueryUnbufferedAsync(sql))
    {
        FiddleHelper.WriteTable(orderDetail);
    }
}
```

In this case, each `orderDetail` is of type `dynamic`. You can access its columns by name without defining a class.

---

## Example - QueryUnbufferedAsync Strongly Typed

If you prefer strongly typed objects, just specify the type parameter. Dapper maps each row as it is read.

```csharp
public class OrderDetail
{
    public int OrderDetailID { get; set; }
    public int OrderID { get; set; }
    public int ProductID { get; set; }
    public int Quantity { get; set; }
}

string sql = "SELECT TOP 10 * FROM OrderDetails";

using (var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServerW3Schools()))
{
    await foreach (var orderDetail in connection.QueryUnbufferedAsync<OrderDetail>(sql))
    {
        Console.WriteLine($"{orderDetail.OrderDetailID} - {orderDetail.Quantity}");
    }
}
```

Here you stream each `OrderDetail` row without buffering the entire list in memory.

---

## Example - QueryUnbufferedAsync Multi-Mapping (One-to-One)

You can also stream results for one-to-one relationships.

Example: fetching `Invoice` with its related `InvoiceDetail`.

```csharp
string sql = "SELECT * FROM Invoice AS A INNER JOIN InvoiceDetail AS B ON A.InvoiceID = B.InvoiceID;";

using (var connection = My.ConnectionFactory())
{
    var invoices = new List<Invoice>();

    await foreach (var invoice in connection.QueryUnbufferedAsync<Invoice, InvoiceDetail, Invoice>(
        sql,
        (inv, detail) =>
        {
            inv.InvoiceDetail = detail;
            return inv;
        },
        splitOn: "InvoiceID"))
    {
        invoices.Add(invoice);
    }
}
```

The `splitOn` tells Dapper where to break the row between the two mapped objects.

---

## Example - QueryUnbufferedAsync Multi-Mapping (One-to-Many)

For one-to-many relationships, you can stream results as well.

Example: fetching `Orders` with their related `OrderDetails`.

```csharp
string sql = "SELECT TOP 10 * FROM Orders AS A INNER JOIN OrderDetails AS B ON A.OrderID = B.OrderID;";

using (var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServerW3Schools()))
{
    var orderDictionary = new Dictionary<int, Order>();

    await foreach (var order in connection.QueryUnbufferedAsync<Order, OrderDetail, Order>(
        sql,
        (o, detail) =>
        {
            if (!orderDictionary.TryGetValue(o.OrderID, out var entry))
            {
                entry = o;
                entry.OrderDetails = new List<OrderDetail>();
                orderDictionary.Add(entry.OrderID, entry);
            }

            entry.OrderDetails.Add(detail);
            return entry;
        },
        splitOn: "OrderID"))
    {
        // Ensure no duplicates by dictionary tracking
    }

    var list = orderDictionary.Values.ToList();
    Console.WriteLine(list.Count);
}
```

This way, you can handle very large parent-child datasets efficiently without holding everything in memory.

---

## Related Articles

* [Dapper - Anonymous Result](/result-anonymous)
* [Dapper - Strongly Typed Result](/result-strongly-typed)