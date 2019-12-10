# MySql

## Description

In LINQ to SQL, you can easily interact with regular data structures, XML and SQL Server, but if you want to talk to MySQL, you will need a third-party package to handle the interaction.

### DbLinq

 - DbLinq is the LINQ provider that allows using common databases with an API close to Linq to SQL. 
 - It provides interfaces between LINQ and not only MySQL but some other popular databases as well such as SQLite, Oracle, and PostgreSQL, etc. 
 - To use DbLinq, there are few steps which should be performed before accessing MySql database using LINQ.

### Steps

 1. Download source code from [https://github.com/DbLinq/dblinq2007](https://github.com/DbLinq/dblinq2007)
 2. Extract the zip file and build the **DbMetal** project in Visual Studio
 3. Edit the batch file 'src\DbMetal\run_myMetal.bat' to generate a C# file representing your database.

```charp
DbMetal.exe -provider=MySql -database:customerdb -server:localhost -user:root -password: -namespace:LinqToMySql -code:Customer.cs -sprocs
```
 4. Make sure to enter your all your information correctly including user id and password.
 5. Now run this batch file, and you will see a new Customer.cs file is generated in the same folder which contains model and a data context classes.
 5. Include that file in your project.
 6. Make sure to add DbLinq.dll and Dblinq.MySql.dll from the source code binaries.
 7. Now you can access MySql database using LINQ.

```csharp
using (MySqlConnection connection = new MySqlConnection("server=localhost;user id=root;persistsecurityinfo=True;database=customerdb;SslMode=none"))
{
    var db = new CustomerDb(connection);
    var customers = db.Customers;

    foreach (var customer in customers)
    {
        Console.WriteLine(customer.FirstName + ", " + customer.LastName);
    }
}

```
