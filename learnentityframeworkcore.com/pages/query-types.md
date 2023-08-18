---
title: Entity Framework Core Query Types
description: Query Types represent non-entity types that can be returned from database queries that are mapped to database tables or views without an identity column. 
canonical: query-types
status: Published
lastmod: 2023-02-11
---

# EF Core Query Type

_This feature was added in EF Core 2.1_

Query types are non-entity types (classes) that form part of the conceptual model and can be mapped to tables and views that don't have an identity column specified, or to a `DbQuery` type. As such, they can be used in several scenarios:

- They can represent ad hoc types returned from [`FromSql` method calls](/raw-sql#dbset.fromsql)
- They enable mapping to views
- They enable mapping to tables that do not have an Identity column  

Importantly, since they do not need to have a key value specified, query types do not participate in [change tracking](/dbcontext/change-tracker) and cannot take part in `Add`, `Update` or `Delete` operations. They essentially represent read-only objects.

## Mapping to DbQuery

The `DbQuery` type was introduced in EF Core 2.1. along with query types. A `DbQuery` is a property on the `DbContext` that acts similarly to a `DbSet`, providing a root for LINQ queries. However, it doesn't enable operations that write to the database e.g. `Add`. The `DbQuery` maps to a table or view. To illustrate how the `DbQuery` works, here is a simple model representing customers and their orders:


```csharp
public class Customer
{
    public int CustomerId { get; set; }
    public string Name { get; set; }
    public ICollection<Order> Orders { get; set; } = new HashSet<Order>();
}

public class Order
{
    public int OrderId { get; set; }
    public DateTime DateCreated { get; set; }
    public int CustomerId { get; set; }
    public Customer Customer { get; set; }
    public ICollection<OrderItem> OrderItems { get; set; } = new HashSet<OrderItem>();
}

public class OrderItem
{
    public int OrderItemId { get; set; }
    public string Item { get; set; }
    public decimal Price { get; set; }
}
```

In the real world, the model will have a lot more properties and any queries against the respective `DbSet` objects will return all columns of data. There will be occasions where just a subset of columns are required, as defined in the following database view named _OrderHeaders_:

```sql
create view OrderHeaders as
    select      c.Name as CustomerName, 
                o.DateCreated, 
                sum(oi.Price) as TotalPrice, 
                count(oi.Price) as TotalItems
    from        OrderItems  oi 
                inner join Orders o on oi.OrderId = o.OrderId
                inner join Customers c on o.CustomerId = c.CustomerId
    group by oi.OrderId, c.Name, o.DateCreated
```
The data returned from calling the view is represented by the following query type:

```csharp
public class OrderHeader
{
    public string CustomerName { get; set; }
    public DateTime DateCreated { get; set; }
    public int TotalItems { get; set; }
    public decimal TotalPrice { get; set; }
}
```

The `OrderHeader` class is mapped to the view via the `DbQuery<OrderHeader>` property that's added to the `DbContext` along with the `DbSet` properties for the orders, order items and the customers:


```csharp
public class SampleContext : DbContext
{
    public DbSet<Order> Orders { get; set; }
    public DbSet<OrderItem> OrderItems { get; set; }
    public DbSet<Customer> Customers { get; set; }

    public DbQuery<OrderHeader> OrderHeaders { get; set; }

    ...
}
```
This is all that is required to enable querying of data in the same way as if the `OrderHeaders` property was a `DbSet`:

```csharp
var orderHeaders = db.OrderHeaders.ToList();
```
As with normal `DbSet` queries, you can also specify filter criteria:

```csharp
var orderHeaders = db.OrderHeaders.Where(x => x.TotalItems > 15).ToList();
```


## Configuration

If you don't want to clutter up your `DbContext` with multiple `DbQuery` properties as well as the `DbSet` declarations, you have two options. One is to make your `DbContext` class a `partial` class and then split the `DbQuery` declarations into a separate file:

```csharp
[SampleContext.cs]
public partial class SampleContext : DbContext
{
    public DbSet<Order> Orders { get; set; }
    public DbSet<OrderItem> OrderItems { get; set; }
    public DbSet<Customer> Customers { get; set; }

    ...
}
```

```csharp
[SampleContextDbQuery.cs]
public partial class SampleContext : DbContext
{
    public DbQuery<OrderHeader> OrderHeaders { get; set; }
    public DbQuery<OrderTotal> OrderTotals { get; set; }
    ...
}
```
The other option is to use configuration to include the query objects in the conceptual model using the `ModelBuilder.Query` method:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Query<OrderHeader>().ToView("OrderHeaders");
}
``` 
Now there is no `DbQuery` type object to base your query from, so you use the `DbContext.Query<TEntity>` method instead:

```csharp
var orderHeaders = db.Query<OrderHeader>().ToList();
```
## Relationships

Query types can take part in relationships as navigational property, but the query type cannot form the principal in the relationship. The key property of the principal end of the relationship needs to be included in the table or view represented by the query type so that a SQL join can be made:

```sql
select      c.Name as CustomerName, 
            c.CustomerId,
            o.DateCreated, 
            sum(oi.Price) as TotalPrice, 
            count(oi.Price) as TotalItems
from        OrderItems  oi 
            inner join Orders o on oi.OrderId = o.OrderId
            inner join Customers c on o.CustomerId = c.CustomerId
group by oi.OrderId, c.Name, o.DateCreated
```

Then the `Customer` entity must be included as a navigational property:

```csharp
public class OrderHeader
{
    public string CustomerName { get; set; }
    public DateTime DateCreated { get; set; }
    public int TotalItems { get; set; }
    public decimal TotalPrice { get; set; }
    public Customer Customer { get; set; }
}
```

This is enough to enable the `Include` method to eager load the principal entity:

```csharp
var orderHeaders = db.Query<OrderHeader>().Include(o => o.Customer).ToList();
```

## `FromSql`

You can use a query type to return non-entity types from `FromSql` method calls more efficiently than the [previous approach that required you to query an entity type and then project a subset of the properties to a different type](/raw-sql#non-entity-types-and-projections).

The query type must be registered with the `DbContext` as a `DbQuery` and you must include columns in the SQL to match all of the properties in the query type:

```csharp
var orderHeaders = db.OrderHeaders.FromSql(
                    @"select c.Name as CustomerName, o.DateCreated, sum(oi.Price) as TotalPrice, 
                    count(oi.Price) as TotalItems
                    from  OrderItems  oi 
                    inner join Orders o on oi.OrderId = o.OrderId
                    inner join Customers c on o.CustomerId = c.CustomerId
                    group by oi.OrderId, c.Name, o.DateCreated");
```
If one of the properties of the query type is a complex type, such as the `Customer` property in the previous example, you must include columns for all of the complex type's properties, too. 

The main benefit of this approach is that you do not have to create a View or Table in the database to represent a non-entity type. 
