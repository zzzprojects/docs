# Insert

In LINQ to SQL, you can easily insert data into a database by adding objects to `Table<TEntity>` collection and then call `SubmitChanges()` method. LINQ to SQL will translate your changes into the appropriate SQL `INSERT` commands.

The following code creates a customer object and inserts it into the Customers table.

```csharp

using (var db = new CustomerDataContext(@"Data Source=(localdb)\ProjectsV13;Initial Catalog=CustomerDB;"))
{
    var customer = new Customer()
    {
        FirstName = "Maria",
        LastName = "Anders",
        Address = "Obere Str. 57"
    };
    
    db.Customers.InsertOnSubmit(customer);
    
    db.SubmitChanges();
}

```