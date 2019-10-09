# Load Related Data

## Lazy Loading

By default, LINQ to SQL loads the related entities, using Lazy Loading. It means that the related entities are not loaded until you iterate them. 

 - For example, we have a Customer entity, and it contains a list of Invoices. 
 - Now when you retrieve the data from Customers table, then it will execute a query to get the data from the Customers table only and the Invoices data will be retrieved when you iterate the Invoices.  

Let's have a simple example in which we will get all customers and then iterate over each customer and print some data of all the invoices of that particular customer. 

```charp
using (var db = new CustomerDataContext(@"Data Source=(localdb)\ProjectsV13;Initial Catalog=CustomerDB;"))
{
    db.Log = Console.Out;

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

The `db.Log = Console.Out;` will print all the generated SQL queries on a screen to see what is happing.

<img src="https://raw.githubusercontent.com/zzzprojects/LinqToSql-Tutorial/master/docs/images/include1.png">


You can see that the first query will get all the customers and then for every iteration, a new query is executed to fetch invoice data from the database.

Now also look at the value of the parameter of CustomerID, for the first customer it is 1, and for the second it is 2 and so on. Thus, we have one query to load the Customers and two more queries to load the Invoices belonging to that customer.

## Eager Loading

In LINQ to SQL, you can use eager loading where a query for one type of entity also loads related entities as a part of the query. You can get eager load data by using Using `DataLoadOptions`.

```charp
using (var db = new CustomerDataContext(@"Data Source=(localdb)\ProjectsV13;Initial Catalog=CustomerDB;"))
{
    db.Log = Console.Out;

    var dataOptions = new DataLoadOptions();
    dataOptions.LoadWith<Customer>(c => c.Invoices);
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
In this example, we are telling the context that along Customer entites, load the related Invoices as well.

<img src="https://raw.githubusercontent.com/zzzprojects/LinqToSql-Tutorial/master/docs/images/include2.png">

Now you can see that we have only one query and it will load the Customers and the related Invoices data.
