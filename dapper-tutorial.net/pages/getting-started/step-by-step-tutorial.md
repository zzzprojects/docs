---
PermaID: 1000200
Name: step-by-step-tutorial
---

# Step by Step Tutorial 

## What is Dapper?

Dapper is an object-relational mapping (ORM) for the .NET platform. It provides a framework for mapping an object-oriented domain model to a traditional relational database.

 - Dapper provides a mapping between databases and .NET objects. 
 - It owns the title of King of Micro ORM in terms of speed and is as fast as using a raw ADO.NET data reader.
 - It extends the `IDbConnection` by providing useful extension methods to query your database.

## Features

Dapper has the following key features:

 - Speedy and high performance
 - Choice of static/dynamic object binding
 - Easy handling of SQL query
 - Multiple query support
 - Support and easy handling of stored procedures
 - Operating directly on IDbConnection interface
 - Bulk data insert functionality 
 
## How to Install Dapper?
 
To install Dapper using Visual Studio, you can follow these steps:

### Step 1

Create an ASP.NET MVC Web Application project.

### Step 2

Install Dapper through [NuGet](https://www.nuget.org/packages/Dapper) by executing the following command in **Package Manager Console**.

```csharp
PM> Install-Package Dapper
```

## Database Data

For this example, let's assume we have the following customers table in the database.

```csharp
CREATE TABLE [dbo].[Customers] (
    [CustomerID] INT            IDENTITY (1, 1) NOT NULL,
    [FirstName]  NVARCHAR (MAX) NULL,
    [LastName]   NVARCHAR (MAX) NULL,
    [Email]      NVARCHAR (MAX) NULL,
    CONSTRAINT [PK_dbo.Customers] PRIMARY KEY CLUSTERED ([CustomerID] ASC)
);
```

The customers' table contains the following data.

<img src="https://raw.githubusercontent.com/zzzprojects/docs/master/dapper-tutorial.net/images/customer-data.png">

## Create Model
 
The model is a collection of classes to interact with the database. Now let's add an entity class `Customer` in the Model folder.

```csharp
public class Customer
{
    public int CustomerID { get; set; }
    public string FirstName { get; set; }
    public string LastName { get; set; }
    public string Email { get; set; }
}
```
## Create Controller

MVC controllers are responsible for responding to requests made against an ASP.NET MVC website. Let's create a 'CustomerController' with **MVC 5 Controller with read/write actions**.

In Controller `Index` Action, let's add the following code to retrieve all the data from the Customers table.

```csharp
// GET: Customer
public ActionResult Index()
{
    List<Customer> customers = new List<Customer>();
    using (IDbConnection db = new SqlConnection(ConfigurationManager.ConnectionStrings["CustomerConnection"].ConnectionString))
    {

        customers = db.Query<Customer>("Select * From Customers").ToList();
    }
    return View(customers);
}

```

## Create View

Generally, we create View with the same name as an Action method, so let's create an Index view and add the following code.

```csharp
@model IEnumerable<DapperDemo.Models.Customer>

@{
    ViewBag.Title = "Index";
}

<h2>Index</h2>

<p>
    @Html.ActionLink("Create New", "Create")
</p>
<table class="table">
    <tr>
        <th>
            @Html.DisplayNameFor(model => model.FirstName)
        </th>
        <th>
            @Html.DisplayNameFor(model => model.LastName)
        </th>
        <th>
            @Html.DisplayNameFor(model => model.Email)
        </th>
        <th></th>
    </tr>

@foreach (var item in Model) {
    <tr>
        <td>
            @Html.DisplayFor(modelItem => item.FirstName)
        </td>
        <td>
            @Html.DisplayFor(modelItem => item.LastName)
        </td>
        <td>
            @Html.DisplayFor(modelItem => item.Email)
        </td>
        <td>
            @Html.ActionLink("Edit", "Edit", new { id=item.CustomerID }) |
            @Html.ActionLink("Details", "Details", new { id=item.CustomerID }) |
            @Html.ActionLink("Delete", "Delete", new { id=item.CustomerID })
        </td>
    </tr>
}

</table>
```

Let's run your application and specify the following Url.

```csharp
http://localhost:55016/Customer/
```

<img src="https://raw.githubusercontent.com/zzzprojects/docs/master/dapper-tutorial.net/images/view-customer-data.png">

[Try it online](https://dotnetfiddle.net/MyI0LA)
