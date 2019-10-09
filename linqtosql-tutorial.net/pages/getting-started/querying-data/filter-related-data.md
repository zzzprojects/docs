# Filter Related Data

By default, LINQ to SQL loads the related entities, using Lazy Loading, but you can use eager loading where a query for one type of entity also loads related entities as a part of the query. 

Now if you want to load limited related data or filtered related data, you can use the `DataLoadOptions.AssociateWith()` method.

 - You can use the AssociateWith method to specify sub-queries to limit the amount of retrieved data.
 - You can filter EntitySets at the DataContext level, and such filters apply to all queries done with that DataContext instance.

In the following example, DataLoadOptions.AssociateWith(LambdaExpression) is used to filter invoices.

```csharp
using (var db = new CustomerDataContext(@"Data Source=(localdb)\ProjectsV13;Initial Catalog=CustomerDB;"))
{
    db.Log = Console.Out;

    var dataOptions = new DataLoadOptions();
    dataOptions.LoadWith<Customer>(c => c.Invoices);
    dataOptions.AssociateWith<Customer>(
        c => c.Invoices
        .Where(i => i.Date == DateTime.Today));

    db.LoadOptions = dataOptions;

    var customers = db.Customers.ToList();

    foreach (var customer in customers)
    {
        Console.WriteLine(customer.CustomerId + ":");
        foreach (var invoice in customer.Invoices)
        {
            Console.WriteLine("\t" + invoice.Id + ": " + invoice.Date.ToShortDateString());
        }
    }
}
```
