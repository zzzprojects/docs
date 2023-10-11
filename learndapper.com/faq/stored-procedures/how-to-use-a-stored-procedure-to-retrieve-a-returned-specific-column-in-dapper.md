---
id: d3aea7ec-1b98-4472-be46-7fe10e2f2814
position: 4
title: How to use a stored procedure to retrieve a returned specific column in Dapper?
---

To use a stored procedure and retrieve a returned specific column, you have 3 choices:

1. By mapping returned data to an anonymous type and select your column:

```csharp
using (var connection = new SqlConnection("connectionString"))
{
    var result = connection.Query("MyStoredProcedure", commandType: CommandType.StoredProcedure).ToList();

    var specificColumnList = result.Select(x => (string)x.MyColumn).ToList();
}
```

2. By mapping returned data to a strongly typed object and select your column:

```csharp
public class MyStoredProcedureResult
{
    public string MyColumn { get; set; }
	
    // Add other properties as needed, to match all columns returned by the stored procedure
}
	
using (var connection = new SqlConnection("connectionString"))
{
    var result = connection.Query<MyStoredProcedureResult>("MyStoredProcedure", commandType: CommandType.StoredProcedure).ToList();

    var specificColumnList = result.Select(x => x.MyColumn).ToList();
}
```

3. By using [QueryMultiple (Multi-Mapping)](https://www.learndapper.com/dapper-query/selecting-multiple-results) and read returned data with an anonymous type or strongly typed object:

```csharp
using (var connection = new SqlConnection("connectionString"))
{
    using (var multi = connection.QueryMultiple("MyStoredProcedure", commandType: CommandType.StoredProcedure))
    {
        var firstResultSet = multi.Read().ToList();
        var specificColumnFromFirstResultSet = firstResultSet.Select(x => (string)x.MyColumn).ToList();
    }
}
```

:::{.alert .alert-warning}
NOTE: All columns are returned from the stored procedure, not only the desired selected column.
:::