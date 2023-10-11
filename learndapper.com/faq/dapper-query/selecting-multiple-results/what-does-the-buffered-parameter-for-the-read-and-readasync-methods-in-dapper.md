---
id: 8a831d4e-1659-4c07-9dda-c7673a50437d
position: 2
title: What does the buffered parameter for the Read and ReadAsync methods in Dapper?
---

In Dapper, the [buffered parameter](https://www.learndapper.com/misc/buffered-unbuffered) on the `Read` and `ReadAsync` methods indicates whether the data should be buffered (loaded into memory at once) or unbuffered (streamed one row at a time). By default, this parameter is `true`, meaning data is buffered. If set to `false`, data is read row by row, which can reduce memory usage for large result sets.

```csharp
string sql = "SELECT * FROM Customer; SELECT * FROM Order";

using (var connection = new SqlConnection(connectionString))
{
    using (var multi = connection.QueryMultiple(sql))
    {
        var customers = multi.Read<Customer>(buffered: false);
        var orders = multi.Read<Order>(buffered: false);
    }
}
```