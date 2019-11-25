# Bulk Insert

## Description

The `BulkInsert` method lets you insert a large number of entities in your database.

```csharp
// Easy to use
bulk.DestinationTableName = "Customers";
bulk.BulkInsert(customers);

// Easy to customize
bulk.DestinationTableName = "Customers";
bulk.InsertIfNotExists = true;
bulk.AutoMapOutputIdentity = true;
bulk.BulkInsert(customers);
```

Try it (DataTable): [.NET Core](https://dotnetfiddle.net/gf6m8T) | [.NET Framework](https://dotnetfiddle.net/UtvblA)

Try it (Entity): [.NET Core](https://dotnetfiddle.net/35OrVF) | [.NET Framework](https://dotnetfiddle.net/Nm4Ndu)


### Performance View

| Operations      | 1,000 Entities | 2,000 Entities | 5,000 Entities |
| :-------------- | -------------: | -------------: | -------------: |
| BulkInsert      | 50 ms          | 55 ms          | 75 ms          |

Try this benchmark (DataTable): [.NET Core](https://dotnetfiddle.net/u20yAQ) | [.NET Framework](https://dotnetfiddle.net/op4qjQ)

Try this benchmark (Entity): [.NET Core](https://dotnetfiddle.net/tpdKja) | [.NET Framework](https://dotnetfiddle.net/cHdVFF)

> HINT: A lot of factors might affect the benchmark time such as index, column type, latency, throttling, etc.

### Scenarios
The `BulkInsert` method is **fast** but also **flexible** to let you handle various scenarios such as:
- [Insert and keep identity value](#insert-and-keep-identity-value)
- [Insert and include/exclude properties](#insert-and-includeexclude-properties)
- [Insert only if the entity not already exists](#insert-only-if-the-entity-not-already-exists)
- [Insert with returning identity value](#insert-with-returning-identity-value)
- [More scenarios](#more-scenarios)

### Advantages
- Easy to use
- Flexible
- Increase performance
- Increase application responsiveness

## Getting Started

### Bulk Insert
The `BulkInsert` and `BulkInsertAync` let you insert a large number of entities in your database.

```csharp
bulk.BulkInsert(customers);

bulk.BulkInsertAsync(customers, cancellationToken);
```
Try it (DataTable): [.NET Core](https://dotnetfiddle.net/ZsArtr) | [.NET Framework](https://dotnetfiddle.net/V7BSkx)

Try it (Entity): [.NET Core](https://dotnetfiddle.net/Oaf9dz) | [.NET Framework](https://dotnetfiddle.net/ltMk9u)

### Bulk Insert with options
The `options` parameter lets you customize the way entities are inserted.

```csharp
bulk.BatchSize = 100;
bulk.BulkInsert(customers);

bulk.PrimaryKeyExpression = customer => customer.Code;
bulk.InsertIfNotExists = true;
bulk.BulkInsert(customers);
```
Try it (DataTable): [.NET Core](https://dotnetfiddle.net/YdMN18) | [.NET Framework](https://dotnetfiddle.net/C8kAfL)

Try it (Entity): [.NET Core](https://dotnetfiddle.net/HX11tc) | [.NET Framework](https://dotnetfiddle.net/YzSPKX)

## Real-Life Scenarios

### Insert and keep identity value
Your entity has an identity property, but you want to force it to insert a specific value instead. The `InsertKeepIdentity` option allows you to keep the identity value of your entity.

```csharp
bulk.DestinationTableName = "Customers";
bulk.InsertKeepIdentity = true;
bulk.BulkInsert(customers);
```
Try it (DataTable): [.NET Core](https://dotnetfiddle.net/Ky1t6K) | [.NET Framework](https://dotnetfiddle.net/GwWGpY)

Try it (Entity): [.NET Core](https://dotnetfiddle.net/OMlv89) | [.NET Framework](https://dotnetfiddle.net/04NuC3)

### Insert and include/exclude properties

You want to insert your entities but only for specific properties.

- `ColumnInputExpression`: This option lets you choose which properties to map.
- `IgnoreOnInsertExpression`: This option lets you ignore properties that are auto-mapped.

```csharp
bulk.ColumnInputExpression = c => new { c.CustomerID, c.Name};
bulk.BulkInsert(customers);
            
bulk.IgnoreOnInsertExpression = c => new { c.ColumnToIgnore };
bulk.BulkInsert(customers);
```
Try it (DataTable): [.NET Core](https://dotnetfiddle.net/gk9meY) | [.NET Framework](https://dotnetfiddle.net/xS44Il)

Try it (Entity): [.NET Core](https://dotnetfiddle.net/b5BDTA) | [.NET Framework](https://dotnetfiddle.net/obTRqp)

### Insert only if the entity not already exists
You want to insert entities but only those that don't already exist in the database.

- `InsertIfNotExists`: This option lets you insert only entity that doesn't already exist.
- `PrimaryKeyExpression`: This option lets you customize the key to use to check if the entity already exists or not. This option disables the Auto Mapping.
- `AutoMapKeyExpression`: This option lets you customize the key with expression and keep the Auto Mapping.
- `AutoMapKeyName`: This option lets you customize the key by names and keep the Auto Mapping.

```csharp
bulk.InsertIfNotExists = true;
bulk.AutoMapKeyExpression = c => c.Code;
bulk.BulkInsert(customers);
```
Try it (Entity): [.NET Core](https://dotnetfiddle.net/lKvdhk) | [.NET Framework](https://dotnetfiddle.net/DLMhLv)

```csharp
bulk.InsertIfNotExists = true;
bulk.AutoMapKeyName = "Code";
bulk.BulkInsert(customers);
```
Try it (DataTable): [.NET Core](https://dotnetfiddle.net/BIxSXi) | [.NET Framework](https://dotnetfiddle.net/waYK0E)


### Insert related child entities
You want to insert related child entities.

```csharp
bulk.AutoMapOutputIdentity = true;
bulk.BulkInsert(invoices);

// SET foreign key value            
invoices.ForEach(x => x.Items.ForEach(y => y.InvoiceID = x.InvoiceID));
bulk.BulkInsert(invoices.SelectMany(x => x.Items).ToList());
```
Try it (Entity): [.NET Core](https://dotnetfiddle.net/hlSqpt) | [.NET Framework](https://dotnetfiddle.net/9REv9u)

Try it (DataTable): [.NET Core](https://dotnetfiddle.net/fyfuEf) | [.NET Framework](https://dotnetfiddle.net/zDdjQm)

### Insert with returning identity value
By default, the `BulkInsert` method doesn't return the identity when inserting.

You can return the identity by specifying it should be returned.

```csharp
bulk.AutoMapOutputIdentity = true;
bulk.BulkInsert(customers);
```
Try it (DataTable): [.NET Core](https://dotnetfiddle.net/PyZctu) | [.NET Framework](https://dotnetfiddle.net/g5pSS1)

Try it (Entity): [.NET Core](https://dotnetfiddle.net/ICZEez) | [.NET Framework](https://dotnetfiddle.net/klt6MY)

### More scenarios
Hundred of scenarios has been solved and are now supported.

The best way to ask for a special request or to find out if a solution for your scenario already exists is by contacting us:
info@zzzprojects.com

## Documentation

### BulkInsert

###### Methods

| Name | Description | Example (DataTable) | Example (Entity) |
| :--- | :----------  | :------ | :------ |
| `BulkInsert<T>(items)` | Bulk insert entities in your database. | [Try it](https://dotnetfiddle.net/ikjsmq) | [Try it](https://dotnetfiddle.net/oz7CCC) |
| `BulkInsertAsync<T>(items)` | Bulk insert entities asynchronously in your database. | |
| `BulkInsertAsync<T>(items, cancellationToken)` | Bulk insert entities asynchronously in your database. | |

###### Options
More options can be found here:

- [Audit](https://bulk-operations.net/audit)
- [Batch](https://bulk-operations.net/batch)
- [Execute Event](https://bulk-operations.net/execute-event)
- [Log](https://bulk-operations.net/log)
- [Temporary Table](https://bulk-operations.net/temporary-table)
- [Transient Error](https://bulk-operations.net/transient-error)
- [SQL Server](https://bulk-operations.net/sql-server)
