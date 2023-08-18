---
title: EF Core Bulk Update
description: Entity Framework Extensions BulkUpdate method allows us to perform bulk updates in a single database call. It supports updating all the records in a collection as well as specific records that match certain criteria.
canonical: /bulk-extensions/bulk-update
status: Published
lastmod: 2023-03-02
---

# EF Core Bulk Update

When you are using Entity Framework Core, and you want to update data then you can use the following steps:

 1. **Load the entity from the database into memory:** You can do this using the `Find` or `FirstOrDefault` method of the `DbSet` class.
 2. **Modify the entity's properties:** Once the entity is loaded, you can modify its properties as needed.
 3. **Call SaveChanges:** After making the desired changes to the entity, you need to call the `SaveChanges` method of the `DbContext` to persist the changes to the database.

Here is a basic example of how you can use the Update method of DBContext to perform bulk updates in Entity Framework Core:

```csharp
using (var context = new MyDbContext())
{
    var students = context.Students.Where(s => s.Name == "John").ToList();
    students.ForEach(s => s.Name = "Jane");
    context.UpdateRange(students);
    context.SaveChanges();
}
```

In this example, the `Where` method is used to retrieve a list of students with the name "John". The `ForEach` method is then used to update the `Name` property of each student to "Jane". Finally, the `UpdateRange` method is used to apply the changes to the database, and the `SaveChanges` method is used to persist the changes.

But in this method, a database roundtrip is required every 42 entities (default batch size value).

It is possible to dramatically reduce the number of database roundtrips by using the `BulkUpdate` method from the [Entity Framework Extensions](https://entityframework-extensions.net/) third-party library.

## Bulk Extensions Update

The `BulkUpdate` extension method allows you to perform a bulk update operation, which is a more efficient way of updating multiple records in a database table compared to updating them one by one. It can improve the performance of your application by reducing the number of round trips to the database.

 - The `BulkUpdate` method allows you to specify which properties to update and the criteria for selecting the entities to be updated. 
 - With the `BulkUpdate` method, you can easily update thousands of records at once and make sure that your data is always up-to-date. 

To perform a `BulkUpdate`, you need to pass your entities in the parameters. Here's a simple example of how to use Entity Framework Extensions `BulkUpdate` method:

```csharp
using (var context = new MyDbContext())
{
    var students = context.Students.Where(s => s.Name == "John").ToList();
    students.ForEach(s => s.Name = "Jane");
    context.BulkUpdate(students);
}
```

**Note:** This is just a simple example to illustrate how to use the `BulkUpdate` method. You can customize it based on your specific requirements.

## Why You Should Use BulkUpdate

`BulkUpdate` is recommended for optimizing database performance when updating multiple records at once. It is more efficient than updating records one by one because it reduces the number of database queries and reduces the overhead of establishing database connections. 

 - **Speed:** It can significantly increase the performance of your bulk update operations by using bulk operations instead of single operations.
 - **Easy to use:** The API is very simple and easy to use, allowing you to update large amounts of data with just a few lines of code.
 - **Flexibility:** It provides a lot of options for customizing the bulk update operations to meet your specific needs.

It can save time and improve the overall performance of the application.

## Note

 - NuGet Package: [https://www.nuget.org/packages/Z.EntityFramework.Extensions.EFCore](https://www.nuget.org/packages/Z.EntityFramework.Extensions.EFCore)
 - Documentation: [Entity Framework Extensions – Bulk Update](https://entityframework-extensions.net/bulk-update)