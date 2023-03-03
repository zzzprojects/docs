---
Name: Coalesce Destination
LastMod: 2023-02-28
---

# Coalesce Destination

## Description

The `CoalesceDestination` option lets you update the destination value if the value is null in the database. This option is equivalent of doing a `ISNULL(DestinationTable.ColumnName, StagingTable.ColumnName)` in SQL Server.

### Example

```csharp
context.BulkMerge(customers, options => 
{
	// ON UPDATE, modify the "Name" and "Email" column value only if the destination value is not null
	options.CoalesceDestinationOnMergeUpdateExpression = x => new { x.Name, x.Email };
});
```

## Scenario

A company uses Entity Framework and imports customers with the `BulkMerge` method to insert new customers and update existing customers.

However, there is a particularity. Only null values for customer columns need to be updated; otherwise, we keep the current database values.

In summary:

- When the destination `Email IS NULL`, the destination value is updated
- When the destination `Email IS NOT NULL`, the destination value is keep

## Solution

The`CoalesceDestination` option have 3 solutions to this problem:

- [On[Action]UseCoalesceDestination](#onactionusecoalescedestination)
- [CoalesceDestinationOn[Action]Expression](#coalescedestinationonactionexpression)
- [CoalesceDestinationOn[Action]Names](#coalescedestinationonactionnames)

## On[Action]UseCoalesceDestination

Use this option if you want all properties to use this feature by default.

```csharp		
context.BulkMerge(customers, options => 
{
	// ON UPDATE, modify a column value only if the destination value is not null 
	options.OnMergeUpdateUseCoalesceDestination = true;
});
```

| Method 		  | Name                                       		 | Try it |
|:----------------|:-------------------------------------------------|--------|
| BulkMerge 	  | OnMergeUpdateUseCoalesceDestination 			 | [Fiddle](https://dotnetfiddle.net/QKbtqe) |
| BulkUpdate 	  | OnUpdateUseCoalesceDestination  	   		 	 | [Fiddle](https://dotnetfiddle.net/yyygQl) |
| BulkSynchronize | OnSynchronizeUpdateUseCoalesceDestination		 | [Fiddle](https://dotnetfiddle.net/0UV1zX) |

## CoalesceDestinationOn[Action]Expression

Use this option if you prefer to specify with an expression which properties you want to include.

```csharp
context.BulkMerge(customers, options => 
{
	// ON UPDATE, modify the "Name" and "Email" column value only if the destination value is not null
	options.CoalesceDestinationOnMergeUpdateExpression = x => new { x.Name, x.Email };
});
```

| Method 		  | Name                                       		 | Try it |
|:----------------|:-------------------------------------------------|--------|
| BulkMerge 	  | CoalesceDestinationOnMergeUpdateExpression 		 | [Fiddle](https://dotnetfiddle.net/RMw9fq) |
| BulkUpdate 	  | CoalesceDestinationOnUpdateExpression  	   		 | [Fiddle](https://dotnetfiddle.net/1jfmno) |
| BulkSynchronize | CoalesceDestinationOnSynchronizeUpdateExpression | [Fiddle](https://dotnetfiddle.net/Hacmqr) |

## CoalesceDestinationOn[Action]Names

Use this option if you prefer to specify a list of property names you want to include. The value must correspond to the property name or the navigation name.

```csharp
context.BulkMerge(customers, options => 
{				
	// ON UPDATE, modify the "Name" and "Email" column value only if the destination value is not null
	options.CoalesceDestinationOnMergeUpdateNames = new List<string>() { nameof(Customer.Name), nameof(Customer.Email) };
});
```

| Method 		  | Name                                       		 | Try it |
|:----------------|:-------------------------------------------------|--------|
| BulkMerge 	  | CoalesceDestinationOnMergeUpdateNames 			 | [Fiddle](https://dotnetfiddle.net/AppZ13) |
| BulkUpdate 	  | CoalesceDestinationOnUpdateNames	   			 | [Fiddle](https://dotnetfiddle.net/rBhOY1) |
| BulkSynchronize | CoalesceDestinationOnSynchronizeUpdateNames	   	 | [Fiddle](https://dotnetfiddle.net/AUII0Q) |

## Related Solutions

- [Coalesce](doc-v2/coalesce.md)