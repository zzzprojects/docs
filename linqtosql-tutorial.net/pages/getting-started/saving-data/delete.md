# Delete

In LINQ to SQL, you can easily delete data from a database by removing the corresponding object which is associated with `Table<TEntity>` collection and then call `SubmitChanges()` method. LINQ to SQL will translate your changes into the appropriate SQL `DELETE` commands.

The following code deletes a customer whose first name is "Maria".

```csharp

using (var db = new CustomerDataContext(@"Data Source=(localdb)\ProjectsV13;Initial Catalog=CustomerDB;"))
{
    var customer = db.Customers
        .Where(c => c.FirstName == "Maria")
        .FirstOrDefault();
    
    db.Customers.DeleteOnSubmit(customer);
    
    db.SubmitChanges();
}

```