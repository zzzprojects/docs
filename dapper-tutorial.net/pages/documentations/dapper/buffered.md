---
PermaID: 1000166
Name: Buffered
---

# Dapper - Buffered

## Description

- Default: True

A buffered query return the entire reader at once. That is ideal in most scenario.

A non-buffered query is equivalent as streaming. You only load objects on demand. That can be useful for a very large query to reduce memory usage.

```csharp
string sql = "SELECT * FROM OrderDetails;";

using (var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServerW3Schools()))
{
	var orderDetails = connection.Query<OrderDetail>(sql, buffered: false).ToList();

	FiddleHelper.WriteTable(orderDetails.Take(10));
}
```
Try it: [.NET Core](https://dotnetfiddle.net/m716aX) | [.NET Framework](https://dotnetfiddle.net/gLwGJO)
