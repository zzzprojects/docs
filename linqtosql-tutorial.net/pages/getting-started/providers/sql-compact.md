# SQL Server Compact

## Description

In LINQ to SQL, you can also access SQL Server Compact databases.

```csharp
using (SqlCeConnection conn = new SqlCeConnection(@"Data Source=CustomerDB.sdf"))
{
    using (var db = new CustomerDbDataContext(conn))
    {
        var customers = db.Customers.ToList();
    
        foreach (var customer in customers)
        {
            Console.WriteLine(customer.FirstName + ", " + customer.LastName);
        }
    }
}
```

## Limitations

The SQL Server Compact feature set is much simpler than SQL Server and contains the following limitations that affect LINQ to SQL applications.

 - Does not support stored procedures or views.
 - Supports only a subset of data types and SQL functions.
 - Supports only a subset of SQL constructs.
 - Provides only a minimal optimizer. It is possible that some queries might time out.
 - Does not support partial trust.
