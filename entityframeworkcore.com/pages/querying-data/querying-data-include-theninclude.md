---
permaid: 1000235
Title: EF Core Include - Learn How to Retrieve Related Objects in LINQ
MetaDescription: Unlock the power of EF Core by using Include and ThenInclude to retrieve related entities. Learn how including related objects help to simplify your LINQ query.
LastMod: 2023-02-23
tags: query include
---

# EF Core Include: Discover How to Retrieve Related Objects in LINQ

## Include 

The `Include` method specifies the related objects to include in the query results. It can be used to retrieve some information from the database and also want to include related entities. Now let's say we have a simple model which contains three entities.


```csharp
public class Customer
{
    public int CustomerId { get; set; }
    public string FirstName { get; set; }
    public string LastName { get; set; }
    public string Address { get; set; }
    public virtual List<Invoice> Invoices { get; set; }
}

public class Invoice
{
    public int InvoiceId { get; set; }
    public DateTime Date { get; set; }
    public int CustomerId { get; set; }
    [ForeignKey("CustomerId")]
    public Customer Customer { get; set; }
    public List<InvoiceItem> Items { get; set; }
}

public class InvoiceItem
{
    public int InvoiceItemId { get; set; }
    public int InvoiceId { get; set; }
    public string Code { get; set; }
    [ForeignKey("InvoiceId")]
    public virtual Invoice Invoice { get; set; }
}
```

Now to retrieve all customers and their related invoices we have to use the `Include` Method.


```csharp
using (var context = new MyContext())
{
    var customers = context.Customers
        .Include(c => c.Invoices)
        .ToList();
}
```

## ThenInclude

The `Include` method works quite well for Lists on objects, but what if there is a need for multiple levels of depth. For example, Customer contains a list of invoices and each invoice then contains a list of items. 

EF Core has a new extension method ThenInclude(). You can drill down thru relationships to include multiple levels of related data using the ThenInclude method.


```csharp
using (var context = new MyContext())
{
    var customers = context.Customers
        .Include(i => i.Invoices)
            .ThenInclude(it => it.Items)
        .ToList();
}
```
