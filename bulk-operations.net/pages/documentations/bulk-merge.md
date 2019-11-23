# Bulk Merge

## Description

The `BulkMerge` method lets you merge (insert or update/Upsert) a large number of entities in your database.

```csharp
// Easy to use
bulk.DestinationTableName = "Customers";
bulk.BulkMerge(customers);

// Easy to customize
bulk.DestinationTableName = "Customers";
bulk.BatchSize = 100;
bulk.AutoMapOutputIdentity = true;
bulk.BulkMerge(customers);
```
Try it (Entity): [.NET Core](https://dotnetfiddle.net/istUUT) | [.NET Framework](https://dotnetfiddle.net/qpe8bV)

Try it (DataTable): [.NET Core](https://dotnetfiddle.net/2B7QpT) | [.NET Framework](https://dotnetfiddle.net/rgugIj) 

### Performance Comparison

| Operations      | 1,000 Entities | 2,000 Entities | 5,000 Entities |
| :-------------- | -------------: | -------------: | -------------: |
| BulkMerge       | 80 ms          | 110 ms         | 170 ms         |

Try this benchmark (Entity): [.NET Core](https://dotnetfiddle.net/2zWz3f) | [.NET Framework](https://dotnetfiddle.net/roGRsu)

Try this benchmark (DataTable): [.NET Core](https://dotnetfiddle.net/66unWl) | [.NET Framework](https://dotnetfiddle.net/CY5s3G)

> HINT: A lot of factors might affect the benchmark time such as index, column type, latency, throttling, etc.

### Scenarios
The `BulkMerge` method is **fast** but also **flexible** to let you handle various scenarios such as:

- [Merge and keep identity value](#merge-and-keep-identity-value)
- [Merge with custom key](#merge-with-custom-key)
- [Merge and include/exclude properties](#merge-and-includeexclude-properties)
- [Merge with related child entities](#merge-with-related-child-entities) 
- [More scenarios](#more-scenarios)

### Advantages
- Easy to use
- Flexible
- Increase performance
- Increase application responsiveness

## Getting Started

### Bulk Merge
The `BulkMerge` and `BulkMergeAync` methods let you merge a large number of entities in your database.

```csharp
bulk.BulkMerge(customers);

bulk.BulkMergeAsync(customers, cancellationToken);
```
Try it (Entity): [.NET Core](https://dotnetfiddle.net/gqYYLX) | [.NET Framework](https://dotnetfiddle.net/pigFx8)

Try it (DataTable): [.NET Core](https://dotnetfiddle.net/GxBLPy) | [.NET Framework](https://dotnetfiddle.net/LULDpj) 

### Bulk Merge with options
The `options` parameter lets you use a lambda expression to customize the way entities are inserted/updated.

```csharp
bulk.AutoMapKeyExpression = c => c.Code;
bulk.BulkMerge(customers);
```
Try it (Entity): [.NET Core](https://dotnetfiddle.net/b4N6hA) | [.NET Framework](https://dotnetfiddle.net/5wMQ6X)

```csharp
bulk.AutoMapKeyName = "Code";
bulk.BulkMerge(customers);
```
Try it (DataTable): [.NET Core](https://dotnetfiddle.net/tZQf3s) | [.NET Framework](https://dotnetfiddle.net/JJIPCB)

## Real-Life Scenarios

### Merge and keep identity value
Your entity has an identity property, but you want to force it to insert a specific value instead. The `MergeKeepIdentity` option allows you to keep the identity value of your entity.

```csharp
bulk.MergeKeepIdentity = true;
bulk.BulkMerge(customers);
```
Try it (Entity): [.NET Core](https://dotnetfiddle.net/eHADYK) | [.NET Framework](https://dotnetfiddle.net/52uijH)

Try it (DataTable): [.NET Core](https://dotnetfiddle.net/39CuQl) | [.NET Framework](https://dotnetfiddle.net/gNXl1z) 

### Merge and include/exclude properties
You want to merge your entities but only for specific properties.

- `ColumnInputExpression`: This option lets you choose which properties to map.
- `ColumnIgnoreExpression`: This option lets you ignore properties that are auto-mapped.
- `IgnoreOnMergeInsertExpression`: This option lets you ignore properties only for the `INSERT` part.
- `IgnoreOnMergeUpdateExpression`: This option lets you ignore properties only for the `UPDATE` part.

```csharp
bulk.ColumnInputExpression = c => new { c.CustomerID, c.Name};
bulk.BulkMerge(customers);
            
bulk.IgnoreOnMergeUpdateExpression = c => new { c.CreatedDate };
bulk.BulkMerge(customers);
```
Try it (Entity): [.NET Core](https://dotnetfiddle.net/amdE5y) | [.NET Framework](https://dotnetfiddle.net/W4TJkK)

```csharp
var columnMapping = new ColumnMapping("CreatedDate");
                
columnMapping.IgnoreOnMergeUpdate = true
                    
bulk.ColumnMappings.Add("CustomerID", true);
bulk.ColumnMappings.Add("UpdatedDate");
bulk.ColumnMappings.Add("Name");
bulk.ColumnMappings.Add(columnMapping);
bulk.BulkMerge(dtCustomers);
```
Try it (DataTable): [.NET Core](https://dotnetfiddle.net/hSgAOW) | [.NET Framework](https://dotnetfiddle.net/TIfeSG)

### Merge with custom key
You want to merge entities, but you don't have the primary key. The `ColumnPrimaryKeyExpression` lets you use as a key any property or combination of properties.

```csharp
bulk.AutoMapKeyExpression = c => c.Code;
bulk.BulkMerge(customers);
```
Try it (Entity): [.NET Core](https://dotnetfiddle.net/wSVURw) | [.NET Framework](https://dotnetfiddle.net/Xlcdxq)

```csharp
bulk.AutoMapKeyName = "Code";
bulk.BulkMerge(customers);
```
Try it (DataTable): [.NET Core](https://dotnetfiddle.net/FiSKO1) | [.NET Framework](https://dotnetfiddle.net/9KOxdW) 


### Merge with related child entities
You want to merge entities but also merge related child entities.

```csharp
bulk.DestinationTableName = "Invoices";
bulk.AutoMapOutputIdentity = true;
bulk.BulkMerge(invoices);

// SET foreign key value            
invoices.ForEach(x => x.Items.ForEach(y => y.InvoiceID = x.InvoiceID));

bulk.DestinationTableName = "InvoiceItems";
bulk.BulkMerge(invoices.SelectMany(x => x.Items).ToList());
```
Try it (Entity): [.NET Core](https://dotnetfiddle.net/CnXzHj) | [.NET Framework](https://dotnetfiddle.net/LLDcvy)

Try it (DataTable): [.NET Core](https://dotnetfiddle.net/Y7tDD4) | [.NET Framework](https://dotnetfiddle.net/rhq5ZM) 

### More scenarios
Hundred of scenarios has been solved and are now supported.

The best way to ask for a special request or to find out if a solution for your scenario already exists is by contacting us:
info@zzzprojects.com

## Documentation

### BulkMerge

###### Methods

| Name | Description | Example (DataTable) | Example (Entity) |
| :--- | :----------  | :------ | :------ |
| `BulkMerge<T>(items)` | Bulk insert entities in your database. | [.NET Core](https://dotnetfiddle.net/omnRAu) / [.NET Framework](https://dotnetfiddle.net/hjTQmE) | [.NET Core](https://dotnetfiddle.net/QkE41S) / [.NET Framework](https://dotnetfiddle.net/z2lxbA) |
| `BulkMergeAsync<T>(items)` | Bulk insert entities asynchronously in your database. | | |
| `BulkMergeAsync<T>(items, cancellationToken)` | Bulk insert entities asynchronously in your database. | | |

###### Options
More options can be found here:

- [Audit](https://bulk-operations.net/audit)
- [.NET Core]() / [Batch](https://bulk-operations.net/batch)
- [Execute Event](https://bulk-operations.net/execute-event)
- [Log](https://bulk-operations.net/log)
- [Temporary Table](https://bulk-operations.net/temporary-table)
- [Transient Error](https://bulk-operations.net/transient-error)
- [SQL Server](https://bulk-operations.net/sql-server)
