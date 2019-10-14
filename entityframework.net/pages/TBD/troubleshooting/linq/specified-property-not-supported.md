---
PermaID: 1000157
Name: Specified property not supported
---

# Specified Property Not Supported

## Exception: The specified type member '[ColumnName]' is not supported in LINQ to Entities Exception 

Entity Framework can't filter data based on a property on the server side which is not a part of the table schema. It means that Entity Framework can't convert not mapped a property to SQL.

The following example is trying to filter the customers on TotalInvoices, but the TotalInvoices property is not mapped to database.

```csharp
using (var context = new EntityContext())
{
    var customers = context.Customers
        .Include(c => c.Invoices)
        .Where(c => c.TotalInvoices > 0)
        .ToList();
}
```
[Try it online](https://dotnetfiddle.net/2H8LcC)

### StackOverflow Related Questions

 - [Only initializers, entity members, and entity navigation properties are supported](https://stackoverflow.com/questions/6919709/only-initializers-entity-members-and-entity-navigation-properties-are-supporte)

## Solution

The easiest solution to handle this exception is to filter the customers on the client side when the property is not mapped to the database. 


```csharp
using (var context = new EntityContext())
{
    var customers = context.Customers
        .Include(c => c.Invoices)
        .ToList();

    var list = customers.Where(c => c.TotalInvoices > 0)
        .ToList();
}
```
[Try it online](https://dotnetfiddle.net/dzRVIi)

In this example, the data is first retrieved from the database, and then the data is filtered based on a property which is not mapped to the database.