---
permaid: 1000233
Title: EF Core Client and Server Side Evaluation
MetaDescription: Unlock the power of EF Core by understanding the difference between Client and Server Side Evaluation. Learn when a client-side evaluation is required and why most of the time, a server-side evaluation is preferred.
LastMod: 2023-02-23
tags: query client-side
---

# EF Core Client and Server Side Evaluation

Client vs. Server evaluation is a feature that makes it easier to write queries to the database because Entity Framework Core supports parts of the query being evaluated on the client and parts of it being pushed to the database. 

 - It is up to the database provider to determine which parts of the query will be evaluated in the database. 
 - Client/server evaluation enables queries to contain logic that cannot be evaluated in the database and must be evaluated after the data is retrieved into memory.

In the following example, a helper method is used to combine the first name and last name from a SQL Server database. 


```csharp
private static string CombineNames(string firstName, string lastName)
{
    return firstName + " " + lastName;
}
```

The SQL Server provider has no idea how this method is implemented, so it is not possible to translate it into SQL. All other aspects of the query are evaluated in the database, but combining the first name and last name through this method is performed on the client.


```csharp
var customer = context.Customers
    .Where(c => c.CustomerId == 1)
    .Select(cust => new
    {
        FullName = CombineNames(cust.FirstName, cust.LastName)
    }).FirstOrDefault();
```

Client/Server evaluation has some useful features, but in some cases, it can result in poor performance. In the following query, the helper method is now used in a filter, because this can't be performed in the database.


```csharp
var customers = context.Customers
    .Where(c => CombineNames(c.FirstName, c.LastName)
        .Contains("John"))
    .ToList();
```

All the data is pulled into memory, and then the filter is applied on the client. Depending on the amount of data, and how much of that data is filtered out, this could result in poor performance.

 - By default, EF Core logs a warning when client evaluation is performed. 
 - You can change the behavior in `DbContext.OnConfiguring` when client evaluation occurs to either throw an exception or ignore it.


```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
{
    optionsBuilder.UseSqlServer(@"Data Source=(localdb)\ProjectsV13;Initial Catalog=CustomerDB;")
        .ConfigureWarnings(warnings => warnings.Throw(RelationalEventId.QueryClientEvaluationWarning));
}
```

Now configuration is added in the `DbContext.OnConfiguring` to throw an exception. If a poorly performing query is found, it will throw the following exception with a warning.

<img src="https://raw.githubusercontent.com/zzzprojects/EntityFrameworkCore/master/docs/images/evaluation-client-server.png" alt="Evaluation client server">
