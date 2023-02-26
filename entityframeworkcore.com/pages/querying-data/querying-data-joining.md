---
permaid: 1000236
Title: EF Core Join - Learn How to Combine Row of Many Entities in LINQ
MetaDescription: Unlock the power of EF Core by joining entities to combine rows of multiple tables. Learn how joining multiple entities can help to improve your performance.
LastMod: 2023-02-23
tags: query join
---

# EF Core Join: Discover How to Combine Row of Many Entities in LINQ

## Introduction

In SQL, a `JOIN` clause is used to combine rows from two or more tables, based on a related column between them. In Entity Framework Core you can use the `Join()` and `GroupJoin()` method to achieve the same results.

The following query joins Customers and Invoices table using the `Join()` method. 


```csharp
var query = context.Customers
    .Join(
        context.Invoices,
        customer => customer.CustomerId,
        invoice => invoice.Customer.CustomerId,
        (customer, invoice) => new
        {
            InvoiceID = invoice.Id,
            CustomerName = customer.FirstName + "" + customer.LastName,
            InvoiceDate = invoice.Date
        }
    ).ToList();

foreach (var invoice in query)
{
    Console.WriteLine("InvoiceID: {0}, Customer Name: {1} " + "Date: {2} ",
        invoice.InvoiceID, invoice.CustomerName, invoice.InvoiceDate);
}
```

The following example performs a GroupJoin over the `Invoices` and `InvoiceItems` tables to find all items per invoice.


```csharp
var query = context.Invoices
    .GroupJoin(
        context.InvoiceItems,
        invoice => invoice,
        item => item.Invoice,
        (invoice, invoiceItems) =>
            new
            {
                InvoiceId = invoice.Id,
                Items = invoiceItems.Select(item => item.Code)
            }
        ).ToList();

foreach (var obj in query)
{
    Console.WriteLine("{0}:", obj.InvoiceId);
    foreach (var item in obj.Items)
    {
        Console.WriteLine("  {0}", item);
    }
}
```
