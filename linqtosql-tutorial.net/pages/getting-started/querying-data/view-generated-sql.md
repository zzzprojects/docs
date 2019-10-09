# View Generated SQL

LINQ to SQL follows the following three steps.

 - Translate C# code into SQL statements
 - Execute SQL on a target database
 - Return values back to C# objects.

The simple way of viewing the generated SQL is to use the `DataContext.Log` property to display SQL code in the console window before the code is executed. 

 - You can use this property with a query, insert, update, and delete commands.
 - It can be useful for understanding LINQ to SQL functionality and for debugging specific problems.

```csharp

using (var db = new CustomerContext(@"Data Source=(localdb)\ProjectsV13;Initial Catalog=CustomerContext;"))
{
    db.Log = Console.Out;
    var list = db.Customers.OrderBy(c => c.FirstName).ToList();
}

```

In this example, the Log property display the following generated SQL in the console window.

```csharp

SELECT [t0].[CustomerId], [t0].[FirstName], [t0].[LastName], [t0].[Address]
FROM [Customers] AS [t0]
ORDER BY [t0].[FirstName]

```

