---
permaid: 1000240
Title: EF Core Projection - Learn How to Return an Anonymous Type in LINQ
MetaDescription: Unlock the power of EF Core by using Projection to return anonymous types. Learn how projection can help you to improve your query performance by returning fewer data.
LastMod: 2023-02-23
tags: query projection
---

# EF Core Projection: Discover How to Return an Anonymous Type in LINQ

## Introduction

Projection is a way of translating a full entity into a C# class with a subset of those properties. It is used to create a query that selects from a set of entities in your model but returns results that are of a different type.

 - Projection queries improve the efficiency of your application because only specific fields are retrieved from your database.
 - When you have the data, you can project or filter it as you want the data before output.
 - You can use `Select()` LINQ statement for projection.

### Anonymous Type

Anonymous types provide an easy way to create a new type without initializing them. Projection queries load the data into this Anonymous Type.


```csharp
var customer = context.Customers
    .Select(cust => new
    {
        Id = cust.CustomerId,
        FullName = cust.FirstName + cust.LastName,
        InvoiceList = cust.Invoices
    }).ToList();
```

In this example, the customer data is projected to anonymous type which contains `Id`, `FullName`, and `InvoiceList`.

### Concrete Type

You can also write Projection query to return a Concrete Type, and to do so we will have to create a custom class first, and it must have all properties, which we want to return from the table.



```csharp
public class CustomerData
{
    public int Id { get; set; }
    public string FullName { get; set; }
    public List<Invoice> InvoiceList { get; set; }
}
```

Now change the select clause in the query to map the result to the CustomerData.


```csharp
List<CustomerData> customers = context.Customers
    .Select(cust => new CustomerData
    {
        Id = cust.CustomerId,
        FullName = cust.FirstName + cust.LastName,
        InvoiceList = cust.Invoices
    }).ToList();
```
