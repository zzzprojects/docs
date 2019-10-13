---
PermaID: 1000091
Name: Include with Where Clause
---

# Include With Where Clause

## How to Include with Where clause? 

To retrieve some information from the database and also want to include related entities conditionally. For example, if we have a simple model containing two entities, Customers, and Invoices. Now to retrieve any customer information and also include all the invoices of that customer generated in last seven days.


```csharp
using (var context = new EntityContext())
{
    var fromDate = DateTime.Now.AddDays(-7);

    var customer = context.Customers.Where(c => c.CustomerID == 1)
        .Include(c => c.Invoices)
        .Where(c => c.Invoices.Any(i => i.Date >= fromDate))
        .FirstOrDefault();
}
```

[Try it online](https://dotnetfiddle.net/xT7Foc)

Now when you execute the above example, you will see that it will retrieve the customer with id equal to 1 and will include all the invoices. That is because the where clause is just acting on the customer but not on Invoices.

### StackOverflow Related Questions

 - [EF: Include with where clause](https://stackoverflow.com/questions/16798796/ef-include-with-where-clause)

## Answer

There are different ways to solve this issue, let's use the projection query.


```csharp
using (var context = new EntityContext())
{
    var fromDate = DateTime.Now.AddDays(-7);

    var customer = context.Customers.Where(c => c.Id == 1)
        .Where(c => c.Invoices.Any(i => i.Date >= fromDate))
        .Select(c => new
        {
            c,
            Invoices = c.Invoices.Where(i => i.Date >= fromDate)
        })
        .FirstOrDefault();
}
```
[Try it online](https://dotnetfiddle.net/p7gCwP)

Now you will see that you have an anonymous type which has two properties, c, and Invoices. 

A property called c is of type Customer, and it has all the invoices, the other property Invoices will have only those generated in the last seven days.

## Third Part Library

### Entity Framework Plus

Entity Framework Plus [Query IncludeFilter](http://entityframework-plus.net/query-include-filter) feature allow filtering related entities. This library makes this a lot easier.


```csharp
using (var context = new EntityContext())
{
    var fromDate = DateTime.Now.AddDays(-7);

    var customer = context.Customers.Where(c => c.CustomerID == 1)
            .IncludeFilter(c => c.Invoices.Where(i => i.Date >= fromDate))
            .FirstOrDefault();
}
```

[Try it online](https://dotnetfiddle.net/WdiJTj)

The **IncludeFilter** method works the same as **Include** method but lets you use LINQ Queryable extension methods as part of the query to filter related entities.

[Learn more](http://entityframework-plus.net)