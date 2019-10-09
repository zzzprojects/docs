# Update

In LINQ to SQL, you can easily update data in a database by modifying any property of the object which is associated with `Table<TEntity>` collection and then call `SubmitChanges()` method. LINQ to SQL will translate your changes into the appropriate SQL `UPDATE` commands.

The following code updates the address of a customer whose first name is "Maria".

```csharp

using (var db = new CustomerDataContext(@"Data Source=(localdb)\ProjectsV13;Initial Catalog=CustomerDB;"))
{
    var customer = db.Customers
        .Where(c => c.FirstName == "Maria")
        .FirstOrDefault();
    
    customer.Address = "Obere Street 59";
    
    db.SubmitChanges();
}

```