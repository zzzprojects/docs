# Database First

In LINQ to SQL, it is also possible to reverse engineer an existing database into a DataContext and classes, and it is known as Database First approach. We have a simple database, and it contains one Customer table.

To create a DataContext and your model classes from the existing database, use LINQ to SQL Classes. The easiest way to create these LINQ to SQL classes is by adding a **New Item...** to your project.

<img src="https://raw.githubusercontent.com/zzzprojects/LinqToSql-Tutorial/master/docs/images/database-first.png">

Select LINQ to SQL Classes, call it CustomerDb.dbml and click Add button, the Object Relational Designer appears. 

<img src="https://raw.githubusercontent.com/zzzprojects/LinqToSql-Tutorial/master/docs/images/database-first1.png">

You can drag database tables from the Server Explorer window onto the Object Relational Designer to create LINQ to SQL Classes that represent particular database tables. Let's add the Customers table to the Object Relational Designer.

<img src="https://raw.githubusercontent.com/zzzprojects/LinqToSql-Tutorial/master/docs/images/database-first2.png">

By default, the Object Relational Designer creates a class with the same name as the table name that you drag onto the Designer, but you can easily change it by clicking on the name of the class in the Designer and change the class name. Now save the changes and Object Relational Designer will generate the LINQ to SQL Classes.

Now you have your LINQ to SQL classes, and you can use these classes to retrieve data from the database.

```csharp

using (var db = new CustomerDbDataContext(@"Data Source=(localdb)\ProjectsV13;Initial Catalog=CustomerContextDB;"))
{
    var customers = db.Customers.ToList();

    foreach (var customer in customers)
    {
        Console.WriteLine(customer.FirstName + ", " + customer.LastName);
    }
}
```
