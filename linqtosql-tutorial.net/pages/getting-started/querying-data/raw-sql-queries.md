# Raw SQL Queries

LINQ to SQL allows you to execute raw SQL queries directly against the database where you cannot use LINQ to represent the query and the generated SQL is not efficient enough. 

 - You can use the `DataContext.ExecuteQuery()` method to execute a raw SQL query.
 - LINQ to SQL translates the query into parameterized SQL query and sends it to the SQL server for processing.
 - And then it converts the result of your query directly into objects.

```csharp

using (var db = new CustomerContext(@"Data Source=(localdb)\ProjectsV13;Initial Catalog=CustomerContext;"))
{
    var results = db.ExecuteQuery<Customer>("SELECT * FROM Customers ORDER BY FirstName;");
    
    foreach (var customer in results)
    {
        Console.WriteLine(customer.FirstName + ", " + customer.LastName);
    }
}

```

The ExecuteQuery method also supports a parameterized query.

```csharp
using (var db = new CustomerContext(@"Data Source=(localdb)\ProjectsV13;Initial Catalog=CustomerContext;"))
{
    string firstName = "Maria";
    
    var results = db.ExecuteQuery<Customer>("SELECT * FROM Customers Where FirstName = {0} ORDER BY FirstName;", firstName);
    
    foreach (var customer in results)
    {
        Console.WriteLine(customer.FirstName + ", " + customer.LastName);
    }
}

```
