# Bulk Delete

## Description

The `BulkDelete` method lets you delete a large number of entities in your database.

```csharp
// Easy to use
bulk.DestinationTableName = "Customers";
bulk.BulkDelete(customers);

// Easy to customize
bulk.DestinationTableName = "Customers";
bulk.BatchSize = 100;
bulk.BulkDelete(customers);
```
Try it (DataTable): [.NET Core](https://dotnetfiddle.net/YvsjGI) | [.NET Framework](https://dotnetfiddle.net/XgKaqz)

Try it (Entity): [.NET Core](https://dotnetfiddle.net/9Tzbw1) | [.NET Framework](https://dotnetfiddle.net/O3jY32)


### Performance View

| Operations      | 1,000 Entities | 2,000 Entities | 5,000 Entities |
| :-------------- | -------------: | -------------: | -------------: |
| BulkDelete      | 50 ms          | 55 ms          | 75 ms         |


Try it (DataTable): [.NET Core](https://dotnetfiddle.net/HHLosa) | [.NET Framework](https://dotnetfiddle.net/TknFpT)

Try it (Entity): [.NET Core](https://dotnetfiddle.net/S9wNCs) | [.NET Framework](https://dotnetfiddle.net/CzSZx8)

> HINT: A lot of factors might affect the benchmark time such as index, column type, latency, throttling, etc.

### Scenarios
The `BulkDelete` method is **fast** but also **flexible** to let you handle various scenarios such as:

- [Delete with custom key](#delete-with-custom-key)
- [More scenarios](#more-scenarios)

### Advantages
- Easy to use
- Flexible
- Increase performance
- Increase application responsiveness

## Getting Started

### Bulk Delete
The `BulkDelete` and `BulkDeleteAync` methods your let you delete a large number of entities in your database.
 
```csharp
bulk.BulkDelete(customers);

bulk.BulkDeleteAsync(customers, cancellationToken);
```
Try it (DataTable): [.NET Core](https://dotnetfiddle.net/J86vbg) | [.NET Framework](https://dotnetfiddle.net/Ss93gv)

Try it (Entity): [.NET Core](https://dotnetfiddle.net/Ad7mTs) | [.NET Framework](https://dotnetfiddle.net/p8dt0G) 

### Bulk Delete with options
The `options` parameter lets you usehttps://dotnetfiddle.net/J86vbg a lambda expression to customize the way entities are deleted.

```csharp
bulk.BatchSize = 100;
bulk.BulkDelete(customers);
```
Try it (DataTable): [.NET Core](https://dotnetfiddle.net/SLEovf) | [.NET Framework](https://dotnetfiddle.net/NQlmua)

Try it (Entity): [.NET Core](https://dotnetfiddle.net/vfi0D7) | [.NET Framework](https://dotnetfiddle.net/KLFfCw) 

## Real-Life Scenarios

### Delete with custom key
You want to delete entities, but you don't have the primary key. The `ColumnPrimaryKeyExpression` lets you use as a key any property or combination of properties.

```csharp
bulk.AutoMapKeyName = "Code";
bulk.BulkDelete(dtCustomers.AsEnumerable().Take(2));
```
Try it (DataTable): [.NET Core](https://dotnetfiddle.net/ksZniB) | [.NET Framework](https://dotnetfiddle.net/XKUBto)

```csharp
bulk.AutoMapKeyExpression = customer => customer.Code;
bulk.BulkDelete(customers.Take(2));
```
Try it (Entity): [.NET Core](https://dotnetfiddle.net/7fr6Mk) | [.NET Framework](https://dotnetfiddle.net/yc5tqc) 

### More scenarios
Hundred of scenarios has been solved and are now supported.

The best way to ask for a special request or to find out if a solution for your scenario already exists is by contacting us:
info@zzzprojects.com

## Documentation

### BulkDelete

###### Methods

| Name | Description | Example (DataTable) | Example (Entity) |
| :--- | :----------  | :------ | :------ |
| `BulkDelete<T>(items)` | Bulk delete entities in your database. | [Try it](https://dotnetfiddle.net/srOJJ8) | [Try it](https://dotnetfiddle.net/a6Txdy) |
| `BulkDeleteAsync<T>(items)` | Bulk delete entities asynchronously in your database. | | |
| `BulkDeleteAsync<T>(items, cancellationToken)` | Bulk delete entities asynchronously in your database. | | |

###### Options
More options can be found here:

- [Audit](https://bulk-operations.net/audit)
- [Batch](https://bulk-operations.net/batch)
- [Execute Event](https://bulk-operations.net/execute-event)
- [Log](https://bulk-operations.net/log)
- [Temporary Table](https://bulk-operations.net/temporary-table)
- [Transient Error](https://bulk-operations.net/transient-error)
- [SQL Server](https://bulk-operations.net/sql-server)
