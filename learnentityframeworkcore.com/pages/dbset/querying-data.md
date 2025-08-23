---
title: Querying data via the DbSet
description: The Entity Framework Core DbSet class's role and capabilities in respect of querying data  
canonical: /dbset/querying-data
status: Published
lastmod: 2025-08-23
---

# EF Core Query

Data querying in EF Core is performed against the `DbSet` properties of the `DbContext`. The `DbSet` represents a collection of entities of a specific type - the type specified by the type parameter.

 - Queries are specified using Language Integrated Query (LINQ), a component in the .NET Framework that provides query capability against collections in C# or VB. 
 - LINQ queries can be written using [_query syntax_ or _method syntax_](https://msdn.microsoft.com/en-us/library/bb397947.aspx). Query syntax shares a resemblance with SQL. 
 - The EF Core provider that you use is responsible for translating the LINQ query into the actual SQL to be executed against the database.

The following example uses query syntax to define a query that retrieves all authors ordered by their last name:

```csharp
var data = from a in Authors select a orderby a.LastName
```

You can also use method syntax that uses chained method calls and many of the method names also resemble SQL. The following example shows the previous query expressed using method syntax:

```csharp
var data = context.Authors.OrderBy(a => a.LastName);
```

This guide uses method syntax in query examples. 

## Entity Framework Extensions

Additional methods to retrieve data exist through the [Entity Framework Extensions](https://entityframework-extensions.net/) library.

These methods are designed to efficiently filter or fetch entities from an **existing list**, even in complex scenarios. Compared to the standard `Contains` method in EF Core, they:

* Work with **any kind of list** (basic types, entities, anonymous types, `ExpandoObject`).
* Support **multiple keys** or custom join conditions.
* Handle **millions of items** without SQL limitations.
* Integrate with batch operations like `UpdateFromQuery`, `DeleteFromQuery`, and more.

Here are some of the most popular ones:

* [BulkRead](https://entityframework-extensions.net/bulk-read): Returns **materialized** entities from the database that match items in the list.
* [WhereBulkContains](https://entityframework-extensions.net/where-bulk-contains): Filters a query to include entities from the list (like an efficient `Contains` without limitations).
* [WhereBulkNotContains](https://entityframework-extensions.net/where-bulk-not-contains): Filters a query to exclude entities that are in the list.
* [WhereBulkContainsFilterList](https://entityframework-extensions.net/where-bulk-contains-filter-list): Filters your list and returns only the items that exist in the database.
* [WhereBulkNotContainsFilterList](https://entityframework-extensions.net/where-bulk-not-contains-filter-list): Filters your list and returns only the items that don’t exist in the database.

### Example

```csharp
// Deserialize customers from JSON
var deserializedCustomers = JsonConvert.DeserializeObject<List<Customer>>(json);

// Retrieve only those customers that exist in the database
var customers = context.Customers
    .WhereBulkContains(deserializedCustomers, x => new { x.CustomerID, x.Code })
    .ToList();

// Filter the original list and keep only existing ones
var existingCustomers = context.Customers
    .WhereBulkContainsFilterList(deserializedCustomers, x => x.CustomerID)
    .ToList();
```

### Why Use Them?

A common approach is to write something like this:

```csharp
var customerIds = deserializedCustomers.Select(x => x.CustomerID).ToList();
var customers = context.Customers.Where(x => customerIds.Contains(x.CustomerID)).ToList();
```

While this works, it has **important limitations**:

* Only supports simple types like `int` or `Guid`.
* SQL Server limits the number of items in `IN (...)`.
* Doesn’t support **composite keys** or advanced join logic.

The methods from Entity Framework Extensions solve all of these issues while staying highly efficient.


## Retrieving a single object

Queries that return single entities are performed using variations of the `First`, `FirstOrDefault`, `Single`, `SingleOrDefault`, and `Find` methods:

In addition, there are asynchronous versions of each of the above. 

### First and FirstOrDefault

The `First`, and `FirstOrDefault` methods are intended to be used to return one result from potentially many matches. 

 - If you expect at least one record to match the criteria, you can use the `First` method. 
 - If there is a possibility of no records matching the criteria, use the `FirstOrDefault` method, which will return `null`, the default, in the event of no records being found. 
 - Both of these methods result in _immediate execution_ of the query, meaning that the SQL is generated and executed against the database as soon as the method call is reached. 

The `First` method results in a `SELECT TOP(1)` query fetching all columns from the table that maps to the `DbSet`:
```csharp
var author = context.Authors.First();
```

The above code generates the following resulting SQL query:

```sql
SELECT TOP(1) [a].[AuthorId], [a].[FirstName], [a].[LastName]
FROM [Authors] AS [a]
```

### Single and SingleOrDefault

The `Single` and `SingleOrDefault` methods are used to return a single record where only one should match the criteria specified. 

 - The `Single` method generates a `SELECT TOP(2)` query. 
 - If more than one result is returned by the query, an `InvalidOperationException` is generated with the message:

>Sequence contains more than one element

 - For this reason, you are very unlikely to use the `Single` method without specifying some criteria, usually a unique key or index value. 

You can specify the criteria as a [lambda expression](https://msdn.microsoft.com/en-us/library/bb397687.aspx) in a `Where` method call, or by passing it directly to the `Single ` method call:

```csharp
var author = context.Authors.Where(a => a.AuthorId == 1).Single();

var author = context.Authors.Single(a => a.AuthorId == 1);
```

Both approaches result in identical SQL being generated:

```sql
SELECT TOP(2) [a].[AuthorId], [a].[FirstName], [a].[LastName]
FROM [Authors] AS [a]
WHERE [a].[AuthorId] = 1
```

If it is possible for the query to generate no matching results, you should use the `SingleOrDefault` method which will return `null` in that event.

### Find

The `Find` method in EF Core is used to retrieve an entity based on its primary key. It is a more optimized way of retrieving data compared to a regular LINQ query, as it generates a `WHERE` clause based on the primary key and directly looks up the entity in the database.

 - The `DbSet.Find` method is familiar to users of earlier versions of Entity Framework that support the `DbSet` API. 
 - The method takes the key value(s) of the entity to be retrieved as opposed to a lambda expression, providing a less verbose option for retrieving single entities by their key:

Here's an example of using the Find method in EF Core:

```csharp
var author = context.Authors.Find(1);
```

In the above example, the `Authors` is the table you want to retrieve data from and argument 1 is the primary key of the entity you want to retrieve. If the entity with the specified primary key exists, it will be returned, otherwise, null will be returned.

 - The `Find` method is shorthand (_syntactic sugar_) for the `SingleOrDefault` method, which is why it requires a key value as a parameter. 
 - There should be no possibility of the `Find` method returning multiple results. However, the `Find` method can return `null`.

**Note** that the `Find` method only works if the primary key is defined for the entity. 

## Retrieving multiple objects

In EF Core, you can retrieve multiple objects from a database using LINQ (Language Integrated Query). You can use the `Where` method to filter the data based on a condition, and the `ToList` method to retrieve the data and return it as a list of objects.

 - Queries for retrieving values relating to multiple objects are only executed against a database when the data is iterated over and it is known as _deferred execution_. 
 - Data is iterated over when you use a `foreach` loop, or a finalizing method on the query such as `ToList`, `Sum` or `Count`. 
 - Before that, the LINQ method calls represent the _definition_ of the query to be executed. 

The following example essentially defines a query that will retrieve all products from the database:

```csharp
var products = context.Products; // define query
foreach(var product in products) // query executed and data obtained from database
{
    ...
}
```

The query is not executed until the `foreach` loop is reached. The next example demonstrates the use of `ToList` to force _immediate execution_:

```csharp
var products = context.Products.ToList(); // define query and force execution
```

## Filtering and Ordering

In EF Core, filtering and ordering refer to the process of limiting the data returned from a query based on certain conditions and sorting the data in a specific order. You can filter data in EF Core using the `Where` method and order data using the `OrderBy` or `OrderByDescending` method.

The `Where` method is the principal method for filtering results:

```csharp
var products = context.Products.Where(p => p.CategoryId == 1);
```

The filter criteria are passed into a lambda as an expression that returns a boolean. The expression can include multiple conditions:

```csharp
var products = context.Products.Where(p => p.CategoryId == 1 && p.UnitsInStock < 10);
```

The `OrderBy`, `OrderByDescending`, `ThenOrderBy`, and `ThenOrderByDescending`  methods are used for specifying the order of results:

```csharp
var products = context.Products.OrderBy(p => p.ProductName);
var categories = context.Categories.OrderBy(c => c.CategoryName).ThenOrderBy(c => c.CategoryId);
```

## Grouping

Grouping in EF Core refers to the process of grouping data from a database based on specific criteria. 

 - This can be done by using the `GroupBy` method in LINQ (Language Integrated Query) to group data from a database table based on one or more columns. 
 - The grouped data can then be used for further processing, such as aggregating the data into sums, averages, counts, etc.

The following query produces all products in the database grouped by their `CategoryId` value:

```csharp
var groups = context.Products.GroupBy(p => p.CategoryId);
```

This results in a collection of types that implement the `IGrouping` interface. The types have a `Key` property, which holds the value of the value that was used for grouping i.e. the `CategoryId` value in this case. Each group has a collection of the elements that were selected, so they can be iterated:

```csharp
var groups = context.Products.GroupBy(p => p.CategoryId);
foreach(var group in groups)
{
    //group.Key is the CategoryId value
    foreach(var product in group)
    {
        // you can access individual product properties
    }
}
```

If you want to use multiple properties to group by, you will use an anonymous type to represent the `Key`:

```csharp
var groups = context.Products.GroupBy(p => new {Supplier = p.SupplierId, Country = p.CountryId});
```

Now the elements of the grouping criteria become properties of the `Key`:

```csharp
foreach(var group in groups)
{
    //group.Key.SupplierId is the SupplierId value
    //group.Key.Country is the CountryId value
}
```

**Note:** Grouping is done _in-memory_ in EF Core versions up to 2.1, which means that in the examples above, the data is obtained from the database and then the grouping is performed in the client application by C# code if you are working with older versions of EF Core. The generated SQL orders by the grouping criteria. 

The translation of `GroupBy` was moved to the database in EF Core 2.1.

## Returning non-entity types

In EF Core, you can return non-entity types from your LINQ queries. Non-entity types are classes that are not mapped to any database tables.

 - You can use the `Select` method in LINQ to project the data from your entity types into your custom DTO classes. 
 - This way, you can retrieve only the data you need from the database and shape it into the format that is most convenient for your application.
 - If you only want to return a subset of properties (as opposed to effectively executing a `SELECT *` command), you can project the data into a new form, either as a non-entity type or as an anonymous type. 
 
In the following example, a type named `ProductHeader` is defined specifically to act as a container for a subset of data from the products table:

```csharp
public class ProductHeader
{
    public int ProductId { get; set; }
    public string ProductName { get; set; }
}

List<ProductHeader> headers = context.Products.Select(p => new ProductHeader{
    ProductId = p.ProductId,
    ProductName = p.ProductName
}).ToList();
```

The following code snippet illustrates an anonymous type being used as the container for the data, resulting in a SQL query that only retrieves the `ProductId` and `ProductName` columns from the database:

```csharp
var headers = context.Products.Select(p => new {
    ProductId = p.ProductId,
    ProductName = p.ProductName
}).ToList();
```

As of EF Core 2.1, you can also use [Query Types](/query-types) to return non-entity types.

## Include related data

In EF Core, you can use the `Include` method to load related data along with the main entity data in a single database query. 

 - The `Include` method is used to specify related entities that should be included in the query so that you don't have to issue separate queries for each related entity.
 - The `Include` method is used to eagerly load related data by passing in the navigation property that you want to include in the result set. 

The following query will retrieve all authors and their books:

```csharp
var authors = context.Authors.Include(a => a.Books).ToList();
```
You can chain calls to the Include method to load data from multiple relationships:

```csharp
var authors = context.Authors
    .Include(a => a.Biography)
    .Include(a => a.Books)
    .ToList();
```

You can use the `ThenInclude` method to retrieve data from second and subsequent-level relationships. In the next example, the `Book` entity is assumed to have a navigation property to a `Publisher` entity:

```csharp
var authors = context.Authors
     .Include(a => a.Books)
        .ThenInclude(b => b.Publisher)
     .ToList();
```
The query brings back all authors, their books, and each book's publisher.

The `Include` method is only effective if you return entity types. If you attempt to use the Include method that returns a non-entity type (see previous section), the `Include` will be ignored. It will not form part of the SQL that's generated. 

See also [Lazy Loading](/lazy-loading)

## NoTracking Queries

EF Core `NoTracking` queries refer to Entity Framework Core queries that retrieve data from a database but do not track changes in the context of the returned entities. 

 - It means that when data is retrieved using a `NoTracking` query, EF Core does not keep a record of the retrieved data in its cache, and changes made to the returned entities are not persisted back to the database when the context is saved. 
 - The use of `NoTracking` queries can result in better performance as it reduces the memory usage and overhead associated with tracking changes.

Any entities that your query returns are automatically [tracked by the context](/dbcontext/change-tracker). In cases where the data is read-only i.e. it is being used for display purposes on a web page and will not be modified during the current request, it is not necessary to have the context perform the extra work required to set up tracking. 

The `AsNoTracking` method stops this work from being done and can improve the performance of an application:

```csharp
var cars = context.Cars.AsNoTracking().ToList();
```
If you have a series of read-only queries to perform against the same instance of the context, you can configure the tracking behavior at context-level instead of using the `AsNoTracking` method in each query:

```csharp
using (var context = new SampleContext())
{
    context.ChangeTracker.QueryTrackingBehavior = QueryTrackingBehavior.NoTracking;
    var cars = context.Cars.ToList();
    var customers = context.Customers.ToList();
    ...
}
```

Non-entity types are not tracked by the context.

## EF Core Like

In Entity Framework Core, the "Like" operator can be used in LINQ queries to filter data based on a specified pattern. The "Like" operator is used to perform pattern matching and can be used with the following wildcard characters:

 - `% (percentage sign)`: matches zero or more characters
 - `_ (underscore)`: matches a single character

For example, the following LINQ query retrieves all rows from the `Customers` table where the `City` column starts with "L":

```csharp
using (var context = new MyDbContext())
{
   var customers = context.Customers
       .Where(c => EF.Functions.Like(c.City, "L%"))
       .ToList();
}
```

In this example, the `EF.Functions.Like` method is used to apply the `Like` operator to the `City` column. The `ToList` method is used to execute the query and retrieve the results as a list of entities.
