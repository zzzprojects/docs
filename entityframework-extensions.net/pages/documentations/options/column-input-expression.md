---
Name: Column Input Expression
LastMod: 2025-06-23
---

# Column Input Expression

## Description

The `ColumnInputExpression` allows you to choose specific properties of an entity on which you want to perform the bulk operations.

The following example inserts data to the database by specifying `Name` and `IsActive` properties using the `BulkInsert` operation.

```csharp
using (var context = new EntityContext())
{
    context.BulkInsert(list, 
        options => options.ColumnInputExpression = c => new 
        { 
            c.Name, 
            c.IsActive 
        }
    );
}
```

Try it: [EF Core](https://dotnetfiddle.net/5wiH5w) | [EF6](https://dotnetfiddle.net/lwF8DZ)

The key is required for operations such as `BulkUpdate` and `BulkMerge`.

```csharp
using (var context = new EntityContext())
{
    List<Customer> list = context.Customers.ToList();
    list.Add(new Customer() { Name ="Customer_C", Description = "Description", IsActive = true });
            
    context.BulkMerge(list, options => 
        options.ColumnInputExpression = c => new 
        {
            c.CustomerID, 
            c.Name, 
            c.IsActive 
        }
    );
}
```
Try it: [EF Core](https://dotnetfiddle.net/ZIHjEx) | [EF6](https://dotnetfiddle.net/NlNP7s)

 - It will insert data into `Name` and `IsActive` columns as specified in `ColumnInputExpression`.
 - The data in the `Description` property is ignored during the bulk operation.
