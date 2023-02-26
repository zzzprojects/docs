---
Permalink: articles/carloscds-ef6-generic-query-with-linq
Title: Creating Generic Queries with LINQ in Entity Framework 4
MetaDescription: Learn how to create reusable codes by creating a generic query method using LINQ in Entity Framework 4. This article provides examples and code snippets to help you get started.
LastMod: 2023-02-24
tags: carloscds article linq query
OriginalLink: https://carloscds.net/2010/07/consultas-genricas-com-linq/
CreatedDate: 2010-07-19
CreatedUserName: Carlos dos Santos
CreatedUserLink: https://carloscds.net/
---

# Creating Generic Queries with LINQ in Entity Framework 4

In the concept of object orientation, we always have in mind to create codes that can be reused within the application or even in different applications. In this context, I have been asked how it is possible to use `LINQ`, which is totally object-oriented and strongly typed, and still create reusable codes.

Taking advantage of this situation, I will show how it is possible to create a query method using `LINQ`, which is used to query practically any entity (table) of your model. We will create a completely generic query, and one of the possible places that you can use this type of method, it is in a consultation routine that you call throughout your system, for example.

For our example, let's create a very simple console project, remembering that we will use Visual Studio 2010 with Entity Framework 4:

<img src="https://carloscds.net/wp-content/uploads/2010/07/image_thumb.png" width="665" height="285" alt="Console Application new project">

Now let's add the Entity Framework model, for this example we will use the `Northwind` database, if you don't have it, download it [here](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/northwind-pubs). To add the model, right-click on your project and go to Add / New Item, or CTRL + SHIFT + A and select Data and then ADO.Net Entity Data Model. Choose 
Generate from a database and create a connection to your `Northwind` database. Select some tables, for example: Categories, Products, Customers:

<img src="https://carloscds.net/wp-content/uploads/2010/07/image_thumb1.png" width="673" height="157" alt="Generic queries with Linq">

After creating the EDMX file, let's go to the Query method:

```csharp
static ObjectQuery<DbDataRecord> Query(string query, ObjectContext ctx)
{
    return new ObjectQuery<DbDataRecord>(query, ctx);
}
```

Note: add the following namespaces:

```csharp
using System.Data.Objects;
using System.Data.Common;
```

It looks like it got complicated, but it is very simple. We are returning a generic object of type `DbDataRecord`, and within the method, we are returning a new ObjectQuery, which allows us to create queries dynamically. Let's see an example of a method call:

```csharp
static void Main(string[] args)
{
    NorthwindEntities ctx = new NorthwindEntities();

    var datas = Query("SELECT c.ProductName, c.UnitPrice FROM Products AS c", ctx);

    foreach (DbDataRecord data in datas)
    {
        Console.WriteLine(data["ProductName"] + " - " + data["UnitPrice"]);
    }
}
```

First, we create the context for the `Northwind` database, and then we call the Query method passing in our query and the context. See that the query looks a lot like an SQL query, and in this case, we are bringing the ProductName and UnitPrice from the Products table.

Then we run a `foreach()` to show the data, using `DbDataRecord` to access the query fields. Let's look at another example, now also bringing data from the Categories table:

```csharp
var datas = Query("SELECT c.ProductName, c.UnitPrice, c.Categories.CategoryName FROM Products AS c" , ctx);

foreach (DbDataRecord data in datas)
{
    Console.WriteLine(data["ProductName"] + " - " + data["UnitPrice"]+" - "+data["CategoryName"]);
}
```

And to end an example with conditional query:

```csharp
var datas = Query("SELECT c.ProductName, c.UnitPrice, c.Categories.CategoryName FROM Products AS c WHERE c.UnitPrice < 10" , ctx);

foreach (DbDataRecord data in datas)
{
    Console.WriteLine(data["ProductName"] + " - " + data["UnitPrice"]+" - "+data["CategoryName"]);
}
```

I believe that this simple example can help in the development of your application and also show the amount of existing features in Entity Framework 4.