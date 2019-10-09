# Joining

In SQL, a `JOIN` clause is used to combine rows from two or more tables, based on a related column between them. In LINQ to SQL, you can use the `Join()` and `GroupJoin()` methods to achieve the same results.

## Join Method

The following query joins Customers and Invoices table using the Join() method.

```csharp

using (var db = new CustomerContext(@"Data Source=(localdb)\ProjectsV13;Initial Catalog=CustomerDB;"))
{
    var query = db.Customers
        .Join(
            db.Invoices,
            customer => customer.CustomerId,
            invoice => invoice.CustomerId,
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
}

```

## GroupJoin Method

The following example performs a GroupJoin over the `Invoices` and `InvoiceItems` tables to find all items per invoice.

```csharp

using (var db = new CustomerContext(@"Data Source=(localdb)\ProjectsV13;Initial Catalog=CustomerDB;"))
{
    var query = db.Invoices
        .GroupJoin(
            db.InvoiceItems,
            invoice => invoice.Id,
            item => item.InvoiceId,
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
}

```
