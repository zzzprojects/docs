---
Permalink: articles/carloscds-ef6-stored-procedure
Title: Meta title: How to use Stored Procedures with Entity Framework Code First
MetaDescription: Entity Framework Code First does not have native support for Stored Procedures, but it is possible to access them through the context's SqlQuery() method. Here's how to do it.
LastMod: 2023-02-24
tags: carloscds article stored-procedure code-first
OriginalLink: https://carloscds.net/2011/08/ef-code-first-acessando-stored-procedures/
CreatedDate: 2011-08-20
CreatedUserName: Carlos dos Santos
CreatedUserLink: https://carloscds.net/
---

# How to Use Stored Procedures with Entity Framework Code First

I'll start this post by stating that Entity Framework Code First does not have native support for Stored Procedures, yet!

But if it is not supported natively, then how is it possible to access them? Through the context's `SqlQuery()` method. But there is a drawback: access is linked to the database provider! Each database provider has a specific way of calling and treat stored procedure parameters.

In our example, we will demonstrate how to access a stored procedure from the [NorthWind](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/northwind-pubs) database. The procedure we are going to use is `CustOrderHist`, which takes the customer code as a parameter and returns the products and quantities purchased by the customer.

So let's create our console project in Visual Studio 2010 and add the references for EF Code First.

<img src="https://carloscds.net/wp-content/uploads/2011/08/image_thumb.png" width="242" height="244" alt="Solution EF Stored Procedures">

To get the stored procedure return, let's create a class that will contain the returned fields:

```csharp
public class ProductsByCustomer
{
    public string ProductName { get; set; }
    public int Quantity { get; set; }
}
```

To obtain the contents of the class, you need to check what the stored procedure returns, and in this case, it returns the names of the products and the quantity purchased.

Before creating our context, let's create the configuration file for the connection string, [see this post on connection strings](/articles/carloscds-ef6-connection-strings).

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
    <connectionStrings>
        <add name="Context" providerName="System.Data.SqlClient" connectionString="Data Source=(local);Initial Catalog=Northwind;Persist Security Info=True;User ID=test;Password=test;Pooling=False;MultipleActiveResultSets=true;" />
    </connectionStrings>
</configuration>
```

Now let's create our context and include the call to the stored procedure:

```csharp
class Context: DbContext
{
    public IEnumerable<CustomerProduct> GetProductsByCustomer(string clientCode)
    {
        SqlParameter customerCode = new SqlParameter("@CustomerCode", SqlDbType.Text);
        customerCode.Value = clientCode;

        return Database.SqlQuery<CustomerProduct>("exec CustOrderHist @customerCode", customerID);
    }
}
```

See that our context does not have the data classes, but they were omitted only for an educational matter.

The drawback here, as we said at the beginning, is that the stored procedure call is linked to the database provider. In the example, we used `SqlParameter()` to create the parameter, as our database is SQL Server. If the database is different, the parameter class would have been different.

Now let's go to the example to make the method call:

```csharp
static void Main(string[] args)
{
    Context ctx = new Context();

    var retProc = ctx.GetProductsByCustomer("VINET");

    foreach (var l in retProc)
    {
        Console.WriteLine("{0} - {1}", l.ProductName, l.Quantity);
    }
}
```

See that it is quite simple, as the method returns an `IEnumerable` that can be traversed by a `foreach()`.

I hope the example is useful, but I would like to make it clear that this is only an alternative way to access stored procedures.