# View ChangeSet

In LINQ to SQL, DataContext object keeps track of all the items that are updated, deleted or inserted into the database.

 - By default, when the state change happens to an entity, it is not reflected in the database immediately, so the state of the entity object and the state of the mapped record become different. 
 - You can view changes tracked by a DataContext by using `GetChangeSet()` method.

```csharp
using (var db = new CustomerDataContext(@"Data Source=(localdb)\ProjectsV13;Initial Catalog=CustomerDB;"))
{
    var addedCustomer = new Customer()
    {
        FirstName = "Maria",
        LastName = "Anders",
        Address = "Obere Str. 57",
    };

    db.Customers.InsertOnSubmit(addedCustomer);

    var deletedCustomer = db.Customers
        .Where(c => c.CustomerId == 105)
        .FirstOrDefault();

    db.Customers.DeleteOnSubmit(deletedCustomer);

    var updatedCustomer = db.Customers
        .Where(c => c.FirstName == "Elizabeth")
        .FirstOrDefault();

    updatedCustomer.Address = "Obere Str. 59";

    var changeSet = db.GetChangeSet();

    Console.WriteLine("Total changes : {0}", changeSet);

    db.SubmitChanges();
}
```