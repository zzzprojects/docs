# Code First

In LINQ to SQL, an object model is mapped to a relational database using attributes or an external mapping file to describe the structure of the relational database. 

 - You can create a new database using the `DataContext.CreateDatabase()` method.
 - This method creates a replica of the database only to the extent of the information encoded in the object model. 
 - You can use the `DataContext.CreateDatabase()` method in any number of scenarios, especially if a known data provider like Microsoft SQL Server is available.

Here is a simple object model which contains a Customer class.

```csharp

public class CustomerContext : DataContext
{
    public Table<Customer> Customers;

    public CustomerContext(string connection) : base(connection) { }
}

[Table(Name = "Customers")]
public class Customer
{
    [Column(IsPrimaryKey = true, IsDbGenerated = true)]
    public int CustomerId { get; set; }

    [Column]
    public string FirstName { get; set; }

    [Column]
    public string LastName { get; set; }

    [Column]
    public string Address { get; set; }
}
```  
The following code create a new database "CustomerContextDB" using the object model and insert a customer record.

```csharp
CustomerContext db = new CustomerContext(@"Data Source=(localdb)\ProjectsV13;Initial Catalog=CustomerContext;");
db.CreateDatabase();

var customer = new Customer()
{
    FirstName = "Maria",
    LastName = "Anders",
    Address = "Obere Str. 57",
};

db.Customers.InsertOnSubmit(customer);
db.SubmitChanges();
```

