---
PermaID: 1000166
Title: Dapper Buffered and Unbuffered Query
MetaDescription: Unlock the power of Dapper by using Buffered and Unbuffered Query. Learn how to return the entire reader at once or load an object on demand by streaming the result.
LastMod: 2023-10-20
---

# Dapper Buffered and Unbuffered Query

## Description

Dapper provides two different ways to execute queries against your database: via a "buffered" or "unbuffered" approach. When you use the buffered approach, Dapper will execute the query and then buffer the entire resultset in memory before returning control to you. 

 - It is convenient if you want to work with the data in memory, for example, to perform some calculations. 
 - If you are only interested in a small subset of the data, or if you want to avoid loading too much data into memory at once, you can use the unbuffered approach. 
 - With this approach, Dapper will stream the results from the database as they are read, and will only keep one row in memory at a time. 
 - It is more efficient, but it means that you can't access all of the data at once - you have to process it as it comes in. 

Both approaches have their use cases, and which one you choose will depend on your specific needs. In general, though, the buffered approach is easier to use and will be more convenient in most cases. 

- Default: True

A buffered query returns the entire reader at once. That is ideal in most scenarios.

A non-buffered query is equivalent to streaming. You only load objects on demand. That can be useful for a very large query to reduce memory usage.

```csharp
string sql = "SELECT * FROM OrderDetails;";

using (var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServerW3Schools()))
{
	var orderDetails = connection.Query<OrderDetail>(sql, buffered: false).ToList();

	FiddleHelper.WriteTable(orderDetails.Take(10));
}
```
Try it: [.NET Core](https://dotnetfiddle.net/m716aX) | [.NET Framework](https://dotnetfiddle.net/gLwGJO)
