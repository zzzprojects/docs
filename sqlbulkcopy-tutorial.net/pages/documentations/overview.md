---
Name: Getting Started
---

# Overview

## Why SqlBulkCopy?

In most of the cases, we need to insert a large number of records into one or more tables in a SQL Server database.

 - Inserting one record using a `SqlCommand` along with an `INSERT INTO` statement at a time is very costly and slow. 
 - We need an efficient solution to insert a large number of records quickly.
 - To achieve a suitable solution, .NET provide a class called `SqlBulkCopy` which is part of the `System.Data.SqlClient` namespace. 

## SqlBulkCopy

The `SqlBulkCopy` class helps us transfer data from one data source to another, whether on a single server or between servers. 

 - There are other ways to load data into a SQL Server table such as `INSERT` statements, but SqlBulkCopy offers a significant performance advantage over them.
 - We can transfer the data with high performance to SQL Server using `SQLBulkCopy.WriteToServer()` method.
 - We can read data from any data source as long as that data can be loaded to DataTable or read by IDataReader.

```csharp
using (var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServer()))
{
    connection.Open();

    using (var sqlBulk = new SqlBulkCopy(connection))
    {
        sqlBulk.DestinationTableName = "Customers";
        sqlBulk.WriteToServer(dt);
    }
}
```

[Try it](https://dotnetfiddle.net/RcnxSe)
