---
PermaID: 1000156
Name: Specified type not supported
---

# Specified Type Not Supported

## Exception: The specified type member '[MemberName]' is not supported in LINQ to Entities

Entity Framework cannot convert some data types to SQL such as DateTime.Date.


```csharp
using (var context = new EntityContext())
{
    var fromDate = DateTime.Now.AddDays(-7).Date;

    var customer = context.Customers.Where(c => c.CustomerID == 1)
        .Include(c => c.Invoices)
        .Where(c => c.Invoices.Any(i => i.InvoiceDate.Date >= fromDate))
        .FirstOrDefault();
}
```
[Try it online](https://dotnetfiddle.net/RKXTuP)

### StackOverflow Related Questions

 - [The specified type member 'Date' is not supported in LINQ](https://stackoverflow.com/questions/28381268/the-specified-type-member-date-is-not-supported-in-linq)
 - [The specified type member 'Date' is not supported in LINQ to Entities. Only initializers, entity members, and entity navigation properties](https://stackoverflow.com/questions/14601676/the-specified-type-member-date-is-not-supported-in-linq-to-entities-only-init)

## Solution

The easiest solution to handle this exception is to use **DbFunctions.TruncateTime** method

```csharp
using (var context = new CustomerContext())
{
    var fromDate = DateTime.Now.AddDays(-7).Date;

    var customer = context.Customers.Where(c => c.CustomerID == 1)
        .Where(c => c.Invoices.Any(i => DbFunctions.TruncateTime(i.InvoiceDate) >= fromDate))
        .Select(c => new
        {
            c,
            Invoices = c.Invoices.Where(i => DbFunctions.TruncateTime(i.InvoiceDate) >= fromDate)
        })
        .FirstOrDefault();
}
```

[Try it online](https://dotnetfiddle.net/YjpKhg)