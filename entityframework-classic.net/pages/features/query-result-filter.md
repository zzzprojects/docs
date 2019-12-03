# Query Result Filter

## Description

The `QueryResultFilter` feature allows you to filter the result of a query. The filter is not performed on a database but in the result returned from the query.

It's always recommended to use the QueryFilter over the QueryResultFilter whenever you can to reduce the number of rows returned from the Database. However, the QueryResultFilter might also be useful if your filter cannot be translated into an expression.

### Example

```csharp
public class EntityContext : DbContext
{
    public EntityContext() : base(FiddleHelper.GetConnectionStringSqlServer())
    {
        // Add your QueryResultFilter here
        this.Configuration.QueryResultFilter.Filter<ISoftDelete>(customer => !customer.IsDeleted || DisplaySoftDelete());
    }
    
    public bool DisplaySoftDelete()
    {
        // Check for example if current user has admin right
        return false;
    }
    
    public DbSet<Customer> Customers { get; set; }
}

var list = context.Customers.ToList();
```

Try it: [NET Core](https://dotnetfiddle.net/0h4JL1) | [NET Framework](https://dotnetfiddle.net/39wJxN)

## Documentation

### QueryResultFilter

###### Properties

| Name | Description | Example |
| :--- | :---------- | :------ |
| `ID` | Gets the `QueryFilter` ID. | [NET Core](https://dotnetfiddle.net/JBtES6) / [NET Framework](https://dotnetfiddle.net/pWIl86) |
| `EntityType` | Gets the `QueryFilter` entity type on which the filter is applied. | [NET Core](https://dotnetfiddle.net/H4Exdo) / [NET Framework](https://dotnetfiddle.net/Scty5l) |
| `IsEnabled` | Gets if the `QueryFilter` is enabled. Use `Enable()` and `Disable()` method to change the state. Always return false if the `QueryFilter` feature is disabled. | [NET Core](https://dotnetfiddle.net/WH66vP) / [NET Framework](https://dotnetfiddle.net/rZzXUv) |

###### Methods

| Name | Description | Example |
| :--- | :---------- | :------ |
| `Enable()` | Enable the `QueryFilter`. | [NET Core](https://dotnetfiddle.net/gEVSU7) / [NET Framework](https://dotnetfiddle.net/R4nKJc) |
| `Disable()` | Disable the `QueryFilter`. | [NET Core](https://dotnetfiddle.net/vsnVxs) / [NET Framework](https://dotnetfiddle.net/27CbSm) |

### QueryResultFilterManager

###### Properties

| Name | Description | Example |
| :--- | :---------- | :------ |
| `IsEnabled` | Gets or sets if the `QueryFilter` feature is enabled. | [NET Core](https://dotnetfiddle.net/Uk0XVb) / [NET Framework](https://dotnetfiddle.net/47jkME) |

###### Methods

| Name | Description | Example |
| :--- | :---------- | :------ |
| `Filter<T>(Expression<Func<T, bool>> filter)` | Filter an entity type using a predicate. | [NET Core](https://dotnetfiddle.net/7VdErj)  / [NET Framework](https://dotnetfiddle.net/zcIngq)|
| `Filter<T>(string id, Expression<Func<T, bool>> filter)` | Filter an entity type using a predicate. The `QueryFilter` will be created with the specified ID. | [NET Core](https://dotnetfiddle.net/6zeiIN) / [NET Framework](https://dotnetfiddle.net/g2Hj0r) |
| `EnableFilter(string id)` | Enable the `QueryFilter` with the specified id.  | [NET Core](https://dotnetfiddle.net/OVQLaN) / [NET Framework](https://dotnetfiddle.net/WSTNTV) |
| `DisableFilter(string id)` | Disable the `QueryFilter` with the specified id. | [NET Core](https://dotnetfiddle.net/ieKaYT) / [NET Framework](https://dotnetfiddle.net/9B3fnF)  |
| `GetFilter(string id)` | Get the `QueryFilter` with the specified id. | [NET Core](https://dotnetfiddle.net/5P0bTw) / [NET Framework](https://dotnetfiddle.net/feGJtz) |
