---
title: Table-Valued Parameters
description: Table-Valued Parameter (TVP) is the new feature of Dapper that help developers to more efficiently pass a set of values to a stored procedure. When used with SQL Server, TVPs can significantly reduce the number of round trips between the application and the database. 
canonical: /table-valued-parameters
status: 
lastmod: 
---

# Table-Valued Parameters

Dapper allows you to pass Table-valued parameters (TVP) to SQL Server. This is useful when you need to send a set of data through the database layer, such as when performing bulk inserts/updates. TVPs have been around since version 2008 and they offer better performance than traditional methods of sending data in batches. 

To use TVPs with Dapper, you need to create a custom type and pass it to the stored procedure. The example below shows how this can be done. First, we need to create a user-defined type in the database: 

```csharp
CREATE TYPE TvpExampleType AS TABLE 
(
    ID INT, 
    Name VARCHAR(50)
)
```

Then we create a stored procedure that accepts this type as an argument: 

```csharp
CREATE PROCEDURE dbo.MyStoredProc 
    (@TvpExampleType TvpExampleType READONLY) 
AS 
BEGIN 
    --Do something 
END
```

Finally, we can set up our C# code to use the TVP: 

```csharp
var tvpExampleType = new List<TvpExampleType>(); 
tvpExampleType.Add(new TvpExampleType {ID = 1, Name = "John" }); 
tvpExampleType.Add(new TvpExampleType {ID = 2, Name = "Steve" }); 
 
var p = new DynamicParameters(); 
p.Add("TvpExampleType", tvpExampleType.AsTableValuedParameter("dbo.TvpExampleType")); 
await connection.ExecuteAsync("dbo.MyStoredProc", p, commandType: CommandType.StoredProcedure);
```


You now have a way to pass a list of data to your stored procedure using Dapper. TVPs are an efficient and powerful way to send data to SQL Server, and they can be used with Dapper easily. 