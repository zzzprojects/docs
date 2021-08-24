# Coalesce

## Description

The `Coalesce` option let you keep the destination value if the source value is null. This option is equivalent of doing a `ISNULL(StagingTable.ColumnName, DestinationTable.ColumnName)` in SQL Server.

### Example

```csharp
context.BulkMerge(customers, options => 
{
	// USE the code as the key expression
	options.ColumnPrimaryKeyExpression = x => x.Code;
	
	// ON UPDATE, modifiy the "Name" and "Email" column only if the source value is not null 
	options.CoalesceOnMergeUpdateExpression = x => new { x.Name, x.Email };
});
```

## Scenario

A company uses Entity Framework and needs to import customers with the `BulkMerge` method to insert new customers and update existing customers.

However, there is a particularity. The customer data to import don't contains all values (some are null). The destination value should be keep if the source value is null.

- When `StagingTable.Email IS NULL`, the destination value should be keep
- When `StagingTable.Email IS NOT NULL`, the destination value should be updated

## Solution

The`Coalesce` option have 2 solutions to this problem:

- [CoalesceOn[Action]Expression](#coalesceonactionexpression)
- [CoalesceOn[Action]Names](#coalesceonactionnames)

## CoalesceOn[Action]Expression

Use this option if you prefer to specify with an expression which properties you want to coalesce.

```csharp
context.BulkMerge(customers, options => 
{
	// USE the code as the key expression
	options.ColumnPrimaryKeyExpression = x => x.Code;
	
	// ON UPDATE, modifiy the "Name" and "Email" column only if the source value is not null 
	options.CoalesceOnMergeUpdateExpression = x => new { x.Name, x.Email };
});
```

| Method 		  | Name                             | Try it |
|:----------------|:---------------------------------|--------|
| BulkMerge 	  | CoalesceOnMergeUpdateExpression  | [Fiddle](https://dotnetfiddle.net/UyQh2O) |
| BulkUpdate 	  | CoalesceOnUpdateExpression  	 | [Fiddle](https://dotnetfiddle.net/IZyujj) |

## CoalesceOn[Action]Names

Use this option if you prefer to specify a list of properties names you want to coalesce. The value must correspond to the property name or the navigation name.

```csharp
context.BulkMerge(customers, options => 
{
	// USE the code as the key expression
	options.ColumnPrimaryKeyExpression = x => x.Code;
	
	// ON UPDATE, modifiy the "Name" and "Email" column only if the source value is not null 
	options.CoalesceOnMergeUpdateNames = new List<string>() { nameof(Customer.Name), nameof(Customer.Email) };
});
```

| Method 		  | Name                       | Try it |
|:----------------|:---------------------------|--------|
| BulkMerge 	  | CoalesceOnMergeUpdateNames | [Fiddle](https://dotnetfiddle.net/BOxIWU) |
| BulkUpdate 	  | CoalesceOnUpdateNames	   | [Fiddle](https://dotnetfiddle.net/96yns2) |

## Related Solutions

- [Coalesce Destination](doc-v2/coalesce-destination.md)