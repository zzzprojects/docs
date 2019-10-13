---
PermaID: 1000092
Name: Include Multiple Levels
---

# Include Multiple Levels

## How to Include multiple levels? 

Eager loading is the process whereby a query for one type of entity also loads related entities as part of the query. Eager loading is achieved by use of the Include method. 

The Include() method works quite well for Lists on objects, but what if there is a need for multiple levels of depth. For example, Customer contains a list of invoices and each invoice then contains a list of items.
### StackOverflow Related Questions

 - [Entity Framework - Include Multiple Levels of Properties](https://stackoverflow.com/questions/10822656/entity-framework-include-multiple-levels-of-properties)
 - [Entity framework linq query Include() multiple children entities](https://stackoverflow.com/questions/3356541/entity-framework-linq-query-include-multiple-children-entities?noredirect=1&lq=1) 

## Answer

### Entity Framework 4.1 to 6

The Include() method allows the required depth of eager loading to be specified by providing Select expressions to the appropriate depth.

#### Using Lambda Expression


```csharp
using (var context = new EntityContext())
{
    var customers = context.Customers
            .Include(i => i.Invoices.Select(it => it.Items))
            .ToList();
}
```

[Try it online](https://dotnetfiddle.net/9AlM9G)

The above example will load all customers, their related invoices, and the items of each invoice.

#### Using String Path

Load all customers, all their related invoices, and all items of each invoice using a string to specify the relationships.


```csharp
using (var context = new EntityContext())
{
    var customers = context.Customers
            .Include("Invoices.Items")
            .ToList();
}
```

[Try it online](https://dotnetfiddle.net/FyHHLD)

### Entity Framework Core

EF Core has a new extension method **ThenInclude()**. You can drill down thru relationships to include multiple levels of related data using the ThenInclude method.


```csharp
using (var context = new MyContext())
{
    var customers = context.Customers
            .Include(i => i.Invoices)
                .ThenInclude(it => it.Items))
            .ToList();
}
```