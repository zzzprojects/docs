---
permaid: 1000214
Title: EF Core Code First - Learn How to Create Model From Code
MetaDescription: Unlock the power of EF Core with Code First approach to create your model from your entity. Learn about the advantages of this approach and how to use code first.
LastMod: 2023-02-21
tags: code-first model approach migration command-line
---

# EF Core Code First: Discover How to Create Model From Code

If you have used Entity Framework in the past, you will probably have heard about the **Code First** approach. 

 - Code First workflow begins with classes that describe the conceptual model and then Entity Framework generate a database from that model automatically.  
 - Code first approach offers most control over the final appearance of the application code and the resulting database.

To start using Entity Framework, use the following steps;

The first step is to create a new project and then add all the required NuGet Packages for EF Core. Once all the NuGet packages are installed, add your conceptual model.


```csharp
public class OrderDetail
{
    public int OrderDetailID { get; set; }
    public int OrderID { get; set; }
    public int ProductID { get; set; }
    public int Quantity { get; set; }
    public Order Order { get; set; }
}

public class Order
{
    public int OrderID { get; set; }
    public int CustomerID { get; set; }
    public int EmployeeID { get; set; }
    public DateTime OrderDate { get; set; }
    public List<OrderDetail> OrderDetails { get; set; }
}
```

It is a very simple model which contains only two classes, `Order` and `OrderDetail` has a one-to-many relationship. The next step is to add a new context class and make sure to inherit from the `DbContext` class


```csharp
public class MyContext : DbContext
{
    public DbSet<OrderDetail> OrderDetails { get; set; }
    public DbSet<Order> Orders { get; set; }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
        optionsBuilder.UseSqlServer(@"Data Source=(localdb)\ProjectsV13;Initial Catalog=StoreDB;");
    }
}
```

In EF Core, the DbContext has a virtual method called onConfiguring which will get called internally by EF Core, and it will also pass in an optionsBuilder instance, and you can use that optionsBuilder to configure options for the DbContext. 

The optionsBuilder has UseSqlServer method; it expects a connection string as a parameter. Once you have a model, you can use migrations to create a database.

Now to create a database using migrations from your model, install the following packages;

  - `Microsoft.EntityFrameworkCore.Tools`
  - `Microsoft.EntityFrameworkCore.Design`

Once these packages are installed, run the following command in Package Manager Console.

 - `Add-Migration Initial` 

This command scaffold a migration to create the initial set of tables for your model. When it is executed successfully, then run the following command.

 - `Update-Database` to apply the new migration to the database. This command creates the database before applying migrations.
 
Now you will see a database in Object Explorer with two tables.

<img src="https://raw.githubusercontent.com/zzzprojects/EntityFrameworkCore/master/docs/images/code-first-db.png" alt="Code first">
