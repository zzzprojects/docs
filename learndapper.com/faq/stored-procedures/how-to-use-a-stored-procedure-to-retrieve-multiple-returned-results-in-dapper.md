---
id: 66c1d086-0624-4aa8-812a-b34e52cb494f
position: 6
title: How to use a stored procedure to retrieve multiple returned results in Dapper?
---

To use a stored procedure and retrieve multiple returned results, you need to use the [QueryMultiple](https://www.learndapper.com/dapper-query/selecting-multiple-results) method and read every results by either mapping it to an anoymous type or a strongly typed object:

```csharp
using (var connection = new SqlConnection("connectionString"))
{
    connection.Open();
    using (var results = connection.QueryMultiple("MyStoredProcedure", commandType: CommandType.StoredProcedure))
    {
        // Anonymous Type
        var resultSet1 = results.Read().ToList();
		
        // Strongly Typed Object
        var resultSet2 = results.Read<ResultType2>().ToList();
    }
}
	
public class ResultType2
{
	public int ID { get; set; }
	public string Name { get; set; }
}
```