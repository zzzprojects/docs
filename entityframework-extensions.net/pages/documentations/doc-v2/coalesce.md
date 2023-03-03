---
Name: Coalesce
LastMod: 2023-02-28
---

# Coalesce

## Description

The `Coalesce` option lets you keep the destination value if the source value is null. This option is equivalent of doing a `ISNULL(StagingTable.ColumnName, DestinationTable.ColumnName)` in SQL Server.

### Example

```csharp
context.BulkMerge(customers, options => 
{
	// ON UPDATE, modify the "Name" and "Email" column value only if the source value is not null 
	options.CoalesceOnMergeUpdateExpression = x => new { x.Name, x.Email };
});
```

## Scenario

A company uses Entity Framework and imports customers with the `BulkMerge` method to insert new customers and update existing customers.

However, there is a particularity. The customer data to import doesn't contain all values (some are null).  For some columns, we want to keep the destination value if the source value is null.

In summary:

- When the source `Email IS NULL`, the destination value is keep
- When the source `Email IS NOT NULL`, the destination value is updated

## Solution

The`Coalesce` option have 3 solutions to this problem:

- [On[Action]UseCoalesce](#onactionusecoalesce)
- [CoalesceOn[Action]Expression](#coalesceonactionexpression)
- [CoalesceOn[Action]Names](#coalesceonactionnames)

## On[Action]UseCoalesce

Use this option if you want all properties to use this feature by default.

```csharp
context.BulkMerge(customers, options => 
{
	// ON UPDATE, modify a column value only if the source value is not null 
	options.OnMergeUpdateUseCoalesce = true;
});
```

| Method 		  | Name                                       		 | Try it |
|:----------------|:-------------------------------------------------|--------|
| BulkMerge 	  | OnMergeUpdateUseCoalesce 		 				 | [Fiddle](https://dotnetfiddle.net/nPOanO) |
| BulkUpdate 	  | OnUpdateUseCoalesce  	   		 				 | [Fiddle](https://dotnetfiddle.net/W6Ijmi) |
| BulkSynchronize | OnSynchronizeUpdateUseCoalesce 					 | [Fiddle](https://dotnetfiddle.net/pfbBXy) |

## CoalesceOn[Action]Expression

Use this option if you prefer to specify with an expression which properties you want to include.

```csharp
context.BulkMerge(customers, options => 
{
	// ON UPDATE, modify the "Name" and "Email" column value only if the source value is not null 
	options.CoalesceOnMergeUpdateExpression = x => new { x.Name, x.Email };
});
```

| Method 		  | Name                             	  | Try it |
|:----------------|:--------------------------------------|--------|
| BulkMerge 	  | CoalesceOnMergeUpdateExpression  	  | [Fiddle](https://dotnetfiddle.net/UyQh2O) |
| BulkUpdate 	  | CoalesceOnUpdateExpression  	 	  | [Fiddle](https://dotnetfiddle.net/IZyujj) |
| BulkSynchronize | CoalesceOnSynchronizeUpdateExpression | [Fiddle](https://dotnetfiddle.net/mQtTtg) |

## CoalesceOn[Action]Names

Use this option if you prefer to specify a list of property names you want to include. The value must correspond to the property name or the navigation name.

```csharp
context.BulkMerge(customers, options => 
{
	// ON UPDATE, modify the "Name" and "Email" column value only if the source value is not null
	options.CoalesceOnMergeUpdateNames = new List<string>() { nameof(Customer.Name), nameof(Customer.Email) };
});
```

| Method 		  | Name                       		 | Try it |
|:----------------|:---------------------------------|--------|
| BulkMerge 	  | CoalesceOnMergeUpdateNames 		 | [Fiddle](https://dotnetfiddle.net/BOxIWU) |
| BulkUpdate 	  | CoalesceOnUpdateNames	   		 | [Fiddle](https://dotnetfiddle.net/96yns2) |
| BulkSynchronize | CoalesceOnSynchronizeUpdateNames | [Fiddle](https://dotnetfiddle.net/mmx8xY) |

## Related Solutions

- [Coalesce Destination](doc-v2/coalesce-destination.md)