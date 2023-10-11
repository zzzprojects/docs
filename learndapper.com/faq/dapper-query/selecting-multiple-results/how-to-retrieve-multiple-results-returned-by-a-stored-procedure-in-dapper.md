---
id: d217efa1-f08b-4bf1-bfd8-1fd095061e6b
position: 1
title: How to retrieve multiple results returned by a stored procedure in Dapper?
---

To retrieve multiple results returned by a [stored procedure](https://www.learndapper.com/stored-procedures), you need to specify the stored procedure name in the command text and specify the command type to `CommandType.StoredProcedure`. Then you can read every result by either mapping to an anonymous type or a strongly typed object:

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