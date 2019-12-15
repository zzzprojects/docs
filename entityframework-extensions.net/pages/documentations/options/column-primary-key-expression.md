# Column PrimaryKey Expression

## Description

The `ColumnPrimaryKeyExpression` allows you to choose a specific property or properties as a key to perform the bulk operations.

The following example uses `Login` and `Password` properties as a key to perform `BulkMerge`.

```csharp
using (var context = new EntityContext())
{
    context.BulkMerge(list, 
        options => options.ColumnPrimaryKeyExpression = customer => new 
        { 
            customer.Login, 
            customer.Password 
        }
    );
}
```
Try it: [EF Core](https://dotnetfiddle.net/FZFpec) | [EF6](https://dotnetfiddle.net/L1Wvep)

It will update those records for which the `Login` and `Password` already exist in the database and all the new records will be inserted to the database. 
