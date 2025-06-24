---
Name: Column Output Expression
LastMod: 2025-06-23
---

# Column Output Expression

## Description

The `ColumnOutputExpression` allows you to choose specific properties in which you want to retrieve data from the database.

The following example uses `IsActive` property in the `ColumnInputExpression` and `Name` and `IsActive` properties in the `ColumnOutputExpression`. 

 - It will insert data only in the `IsActive` field and all other properties will remain `NULL` in the database.
 - In the `ColumnOutputExpression`, `Name` and `IsActive` properties are specified so it will update the specified properties from the database to the list.

```csharp
using (var context = new EntityContext())
{
    context.BulkInsert(list, options => {
        options.ColumnOutputExpression = c => new { c.Name, c.IsActive };
        options.ColumnInputExpression = c => new { c.IsActive };
    });
} 
```

Try it: [EF Core](https://dotnetfiddle.net/m2GVIU) | [EF6](https://dotnetfiddle.net/se3Vjk)

 - Now as a result, you will see the `Name` property is `null`, that is because we have inserted data only for `IsActive`, and the `Name` field remains null in the database.
 - When the list was updated from the database, it replace the value of `Name` and `IsActive` from the database and `Name` is `NULL` to the database.
 - The `Description` property is unchanged because it is not updated from the database.

