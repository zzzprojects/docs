# LINQ Query

LINQ (Language Integrated Query) is a structured query syntax in .NET used to save and retrieve data from different sources. 

 - Traditionally, queries are written as a string without type checking at compile time or IntelliSense support, and you will also need to learn different query language for each type of data source, such as SQL databases, XML documents, and Web services, etc. 
 - LINQ gives us strongly typed compile-time checks on queries that can execute against in-memory data, relational data, and XML data.
 - You can write LINQ queries in C# for SQL Server databases, XML documents, ADO.NET Datasets, and any collection of objects that supports IEnumerable or the generic IEnumerable<T> interface. 
 - Third parties also provide LINQ support for many Web services and other database implementations.

LINQ query supports two different ways to the IEnumerable collection or IQueryable data sources.

 1. Query Syntax
 2. Method Syntax

## Query Syntax

LINQ declarative query syntax is similar to SQL, and you can create a query within your code (C# or VB).

```csharp

using (var db = new CustomerDbDataContext(@"Data Source=(localdb)\ProjectsV13;Initial Catalog=CustomerContextDB;"))
{
    var customers = from customer in db.Customers
                    where customer.FirstName == "John"
                    orderby customer.LastName
                    select customer;

    foreach (var cust in customers)
    {
        Console.WriteLine(cust.FirstName + ", " + cust.LastName);
    }
}

```

## Method Syntax

LINQ query also supports method syntax which uses extension methods. The method syntax is the real way the compiler does LINQ queries. 

```csharp

using (var db = new CustomerDbDataContext(@"Data Source=(localdb)\ProjectsV13;Initial Catalog=CustomerContextDB;"))
{
    var customers = db.Customers
        .Where(c => c.FirstName == "John")
        .OrderBy(c => c.LastName);

    foreach (var cust in customers)
    {
        Console.WriteLine(cust.FirstName + ", " + cust.LastName);
    }
}

```
Query syntax and method syntax are semantically identical, but many people find query syntax simpler and easier to read.