# Bulk Operations

When you want to insert, delete or update hundreds, thousands, or millions of entities using LINQ to SQL `SubmitChanges()` method, you will notice that your application performance is INSANELY slow. 

 - The SubmitChanges() requires one database round-trip for every entity to insert, delete or update. 
 - So if you need to add, remove or update 10000 entities, then 10000 database round-trips will be performed, and your application suffers from performances issues.

To improve your application performance, you can use a 3rd party library [LinqToSql Plus](http://linqtosql-plus.net/). It dramatically improves LinqToSql performances by using [bulk operations](http://linqtosql-plus.net/tutorial-bulk-operations).

## Bulk Operations

[LinqToSql Plus](http://linqtosql-plus.net/) provides the following extension methods which require the minimum database round-trips as compared to SubmitChanges(). By example under the hood for SQL Server, a simple SqlBulkCopy could be performed. These methods give you additional flexibility by allowing to customize options such as primary key, columns, include childs entities and more.

 - BulkInsert
 - BulkUpdate
 - BulkDelete
 - BulkMerge (UPSERT operation)
 - BulkSynchronize

### BulkInsert

It inserts all entities in the database.

```csharp

using (var context = new CustomerDataContext(@"Data Source=(localdb)\ProjectsV13;Initial Catalog=CustomerDB;"))
{
    // Easy to use
    context.BulkInsert(list);

    // Easy to customize
    context.BulkInsert(list, options => options.BatchSize = 100);
}

```
Read more: [BulkInsert](http://linqtosql-plus.net/bulk-insert)

### BulkUpdate

It updates all entities in the database. All rows that match the entity key are considered as existing and are `UPDATED` in the database.

```csharp

using (var context = new CustomerDataContext(@"Data Source=(localdb)\ProjectsV13;Initial Catalog=CustomerDB;"))
{
    // Easy to use
    context.BulkUpdate(list);

    // Easy to customize
    context.BulkUpdate(customers, options => options.ColumnPrimaryKeyExpression = customer => customer.Code);
}

```
Read more: [BulkUpdate](http://linqtosql-plus.net/bulk-update)

### BulkDelete

It deletes all entities from the database. All rows that match the entity key are `DELETED` from the database.

```csharp

using (var context = new CustomerDataContext(@"Data Source=(localdb)\ProjectsV13;Initial Catalog=CustomerDB;"))
{
    // Easy to use
    context.BulkDelete(list);

    // Easy to customize
    context.BulkDelete(customers, options => options.ColumnPrimaryKeyExpression = customer => customer.Code);
}

```
Read more: [BulkDelete](http://linqtosql-plus.net/bulk-delete)

### BulkMerge

A merge is an UPSERT operation. All rows that match the entity key are considered as existing and are `UPDATED`, other rows are considered as new rows and are `INSERTED` in the database.

```csharp

using (var context = new CustomerDataContext(@"Data Source=(localdb)\ProjectsV13;Initial Catalog=CustomerDB;"))
{
    // Easy to use
    context.BulkMerge(list);

    // Easy to customize
    context.BulkMerge(customers, options => options.ColumnPrimaryKeyExpression = customer => customer.Code);
}

```
Read more: [BulkMerge](http://linqtosql-plus.net/bulk-merge)

### BulkSynchronize

It synchronizes all entities to the database. It is a mirror operation from the data source to the database. All rows that match the entity key are `UPDATED`, non-matching rows that exist from the source are `INSERTED`, non-matching rows that exist in the database are `DELETED`.

The database table becomes a mirror of the entity list provided.

```csharp

using (var context = new CustomerDataContext(@"Data Source=(localdb)\ProjectsV13;Initial Catalog=CustomerDB;"))
{
    // Easy to use
    context.BulkSynchronize(list);

    // Easy to customize
    context.BulkSynchronize(customers, options => options.ColumnPrimaryKeyExpression = customer => customer.Code);

```
Read more: [BulkSynchronize](http://linqtosql-plus.net/bulk-synchronize)

### Performance Comparisons

|Operations	    |1,000 Entities	    |2,000 Entities	    |5,000 Entities     |
|:--------------|:------------------|:------------------|:------------------|
|SubmitChanges	|1,000 ms	        |2,000 ms	        |5,000 ms           |
|BulkInsert	    |6 ms	            |10 ms	            |15 ms              |
|BulkUpdate	    |50 ms	            |55 ms	            |65 ms              |
|BulkDelete	    |45 ms	            |50 ms	            |60 ms              |
|BulkMerge	    |65 ms	            |80 ms	            |110 ms             |