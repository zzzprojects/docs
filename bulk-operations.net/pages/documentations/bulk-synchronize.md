# Bulk Synchronize

## Description

The `BulkSynchronize` extension method lets you synchronize a large number of entities in your database.

A synchronize is a mirror operation from the data source to the database. All rows that match the entity key are `UPDATED`, non-matching rows that exist from the source are `INSERTED`, non-matching rows that exist in the database are `DELETED`.

```csharp
// Easy to use
bulk.BulkSynchronize(customers);

// Easy to customize
bulk.DestinationTableName = "Customers";

bulk.AutoMapKeyExpression = customer => customer.Code;
bulk.BulkSynchronize(customers);
```
Try it (Entity): [.NET Core](https://dotnetfiddle.net/NH837U) | [.NET Framework](https://dotnetfiddle.net/06N89k)

```csharp
// Easy to use
bulk.BulkSynchronize(dtCustomers);

// Easy to customize
bulk.DestinationTableName = "Customers";

bulk.AutoMapKeyName = "Code";
bulk.BulkSynchronize(dtCustomers);
});
```
Try it (DataTable): [.NET Core](https://dotnetfiddle.net/hizkul) | [.NET Framework](https://dotnetfiddle.net/RLvbF1)

### Scenarios
The `BulkSynchronize` method is **fast** but also **flexible** to let you handle various scenarios such as:
- [Synchronize and keep identity value](#synchronize-and-keep-identity-value)
- [Synchronize and include/exclude properties](#synchronize-and-includeexclude-properties)
- [Synchronize with custom key](#synchronize-with-custom-key)
- [More scenarios](#more-scenarios)

### Advantages
- Easy to use
- Flexible
- Increase performance
- Increase application responsiveness

## Getting Started

### Bulk Synchronize
The `BulkSynchronize` and `BulkSynchronizeAync` methods let you synchronize a large number of entities in your database.

```csharp
bulk.AutoMapKeyExpression = customer => customer.Code;
bulk.BulkSynchronize(customers);
```
Try it (Entity): [.NET Core](https://dotnetfiddle.net/UQBex7) | [.NET Framework](https://dotnetfiddle.net/rd4Qm2)

```csharp
bulk.AutoMapKeyName = "Code";
bulk.BulkSynchronize(dtCustomers);
```
Try it (DataTable): [.NET Core](https://dotnetfiddle.net/YArxWR) | [.NET Framework](https://dotnetfiddle.net/LMLvFy)

### Bulk Synchronize with options
The `options` parameter lets you use a lambda expression to customize the way entities are synchronized.

```csharp
context.BulkSynchronize(customers, options => options.BatchSize = 100);

context.BulkSynchronize(customers, options => {
    options.ColumnPrimaryKeyExpression = customer => customer.Code;
});
```
Try it (Entity): [.NET Core](https://dotnetfiddle.net/KIWMJk) | [.NET Framework](https://dotnetfiddle.net/P4j6y4)

Try it (DataTable): [.NET Core](https://dotnetfiddle.net/RIkSIh) | [.NET Framework](https://dotnetfiddle.net/Z3QmQH) 

## Real-Life Scenarios

### Synchronize and keep identity value
Your entity has an identity property, but you want to force it to insert a specific value instead. The `SynchronizeKeepIdentity` option allows you to keep the identity value of your entity.

```csharp
bulk.SynchronizeKeepidentity = true;
bulk.BulkSynchronize(customers);
```
Try it (Entity): [.NET Core](https://dotnetfiddle.net/Le84Fc) | [.NET Framework](https://dotnetfiddle.net/P6txGi)

Try it (DataTable): [.NET Core](https://dotnetfiddle.net/nqJGGu) | [.NET Framework](https://dotnetfiddle.net/U8ARaT)

### Synchronize and include/exclude properties
You want to synchronize your entities but only for specific properties.

- `ColumnInputExpression`: This option lets you choose which properties to map.
- `IgnoreOnSynchronizeInsertExpression`: This option lets you ignore when inserting properties that are auto-mapped.
- `IgnoreOnSynchronizeUpdateExpression`: This option lets you ignore when updating properties that are auto-mapped.

```csharp
bulk.IgnoreOnSynchronizeInsertExpression = c => c.UpdatedDate;
bulk.IgnoreOnSynchronizeUpdateExpression = c => c.CreatedDate;
bulk.AutoMapKeyExpression = customer => customer.Code;
bulk.BulkSynchronize(customizeToSynchronize);
```
Try it (Entity): [.NET Core](https://dotnetfiddle.net/6DBVe5) | [.NET Framework](https://dotnetfiddle.net/4u3Mxf)

```csharp
var columnMapping1 = new ColumnMapping("UpdatedDate");
var columnMapping2 = new ColumnMapping("CreatedDate");

columnMapping1.IgnoreOnSynchronizeInsert = true;
columnMapping2.IgnoreOnSynchronizeUpdate = true;
bulk.ColumnMappings.Add(columnMapping1);
bulk.ColumnMappings.Add(columnMapping2);

bulk.ColumnMappings.Add("Code", true);
bulk.ColumnMappings.Add("CustomerID");
bulk.ColumnMappings.Add("FirstName");
bulk.ColumnMappings.Add("LastName");

bulk.BulkSynchronize(dtCustomers);
```
Try it (DataTable): [.NET Core](https://dotnetfiddle.net/TsXcKu) | [.NET Framework](https://dotnetfiddle.net/bAq5hA)  

### Synchronize with custom key
You want to synchronize entities, but you don't have the primary key. The `ColumnPrimaryKeyExpression` lets you use as a key any property or combination of properties.

```csharp
bulk.AutoMapKeyExpression = customer => customer.Code;
bulk.BulkSynchronize(customers);   
```
Try it (Entity): [.NET Core](https://dotnetfiddle.net/AL3HNq) | [.NET Framework](https://dotnetfiddle.net/oEShFh)

```csharp
bulk.AutoMapKeyName = "Code";
bulk.BulkSynchronize(dtCustomers);
```
Try it (DataTable): [.NET Core](https://dotnetfiddle.net/QDn8HG) | [.NET Framework](https://dotnetfiddle.net/SGkrot)  

### More scenarios
Hundred of scenarios has been solved and are now supported.

The best way to ask for a special request or to find out if a solution for your scenario already exists is by contacting us:
info@zzzprojects.com

## Documentation

### BulkSynchronize

###### Methods

| Name | Description | Example (DataTable) | Example (Entity) |
| :--- | :---------- | :------ | :------ |
| `BulkSynchronize<T>(items)` | Bulk synchronize entities in your database. | [.NET Core](https://dotnetfiddle.net/sfq6uc) / [.NET Framework](https://dotnetfiddle.net/DM5xFj) | [.NET Core](https://dotnetfiddle.net/IsPi0j) / [.NET Framework](https://dotnetfiddle.net/LwkmNL) |
| `BulkSynchronizeAsync<T>(items)` | Bulk synchronize entities asynchronously in your database. | |
| `BulkSynchronizeAsync<T>(items, cancellationToken)` | Bulk synchronize entities asynchronously in your database. | |

###### Options
More options can be found here:

- [Audit](https://bulk-operations.net/audit)
- [Batch](https://bulk-operations.net/batch)
- [Execute Event](https://bulk-operations.net/execute-event)
- [Log](https://bulk-operations.net/log)
- [Temporary Table](https://bulk-operations.net/temporary-table)
- [Transient Error](https://bulk-operations.net/transient-error)
- [SQL Server](https://bulk-operations.net/sql-server)
