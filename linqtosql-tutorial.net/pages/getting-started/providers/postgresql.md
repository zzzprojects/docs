# PostgreSQL

## Description

In LINQ to SQL, you can easily interact with regular data structures, XML and SQL Server, but if you want to talk to PostgreSQL, you will need a third-party package to handle the interaction.

### DbLinq

 - DbLinq is the LINQ provider that allows using common databases with an API close to Linq to SQL. 
 - It provides interfaces between LINQ and some popular databases such as MySQL, SQLite, Oracle, and PostgreSQL, etc. 
 - To use DbLinq, there are few steps which should be performed before accessing PostgreSQL database using LINQ.

### Steps

 1. Download source code from [https://github.com/DbLinq/dblinq2007](https://github.com/DbLinq/dblinq2007)
 2. Extract the zip file and build the **DbMetal** project in Visual Studio
 3. Edit the batch file 'src\DbMetal\run_pgMetal.bat' to generate a C# file representing your database.

```charp
DbMetal.exe /provider=PostgreSql /code=CustomerPg.cs -user:postgres -database:postgres -server:localhost -password:yourpassword -namespace:LinqToPg -sprocs
```
 4. Make sure to enter your all your information correctly including user id and password.
 5. Now run this batch file, and you will see a new Customer.cs file is generated in the same folder which contains model and a data context classes.
 5. Include that file in your project.
 6. Make sure to add DbLinq.dll and Dblinq.PostgreSql.dll from the source code binaries.
 7. Now you can access PostgreSQL database using LINQ.

```csharp
using (NpgsqlConnection connection = new NpgsqlConnection(@"host=localhost;database=postgres;user id=postgres;password=yourpassword;"))
{
    var db = new CustomerDb(connection);
    var customers = db.Customers;

    foreach (var customer in customers)
    {
        Console.WriteLine(customer.FirstName + ", " + customer.LastName);
    }
}

```
