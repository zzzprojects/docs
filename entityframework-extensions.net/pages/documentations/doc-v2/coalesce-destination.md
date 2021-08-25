# Coalesce Destination

## Description

The `CoalesceDestination` option let you to update the destination value only if the value is null in the database. This option is equivalent of doing a `ISNULL(DestinationTable.ColumnName, StagingTable.ColumnName)` in SQL Server.

### Example

```csharp
context.BulkMerge(customers, options => 
{
	// USE the code as the key expression
	options.ColumnPrimaryKeyExpression = x => x.Code;
	
	// ON UPDATE, modifiy the "Name" and "Email" column only if the destination value is null 
	options.CoalesceDestinationOnMergeUpdateExpression = x => new { x.Name, x.Email };
});
```

## Scenario

A company uses Entity Framework and needs to import customers with the `BulkMerge` method to insert new customers and update existing customers.

However, there is a particularity. The customer should keep the current `Email` value in the database if already specified.

- When the destination `Email IS NULL`, the destination value should be updated
- When the destination `Email IS NOT NULL`, the destination value should be keep

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
	// USE the code as the key expression
	options.ColumnPrimaryKeyExpression = x => x.Code;
	
	// ON UPDATE, modifiy the "Name" and "Email" column only if the destination value is null 
	options.CoalesceDestinationOnMergeUpdateExpression = x => new { x.Name, x.Email };
});
```

| Method 		  | Name                                       		 | Try it |
|:----------------|:-------------------------------------------------|--------|
| BulkMerge 	  | OnMergeUpdateUseCoalesceDestination 			 | [Fiddle](https://dotnetfiddle.net/nPOanO) |
| BulkUpdate 	  | OnUpdateUseCoalesceDestination  	   		 	 | [Fiddle](https://dotnetfiddle.net/1jfmno) |
| BulkSynchronize | OnSynchronizeUpdateUseCoalesceDestination		 | [Fiddle](https://dotnetfiddle.net/1jfmno) |

## CoalesceDestinationOn[Action]Expression

Use this option if you prefer to specify with an expression which properties you want to include.

```csharp
context.BulkMerge(customers, options => 
{
	// USE the code as the key expression
	options.ColumnPrimaryKeyExpression = x => x.Code;
	
	// ON UPDATE, modifiy the "Name" and "Email" column only if the destination value is null 
	options.CoalesceDestinationOnMergeUpdateExpression = x => new { x.Name, x.Email };
});
```

| Method 		  | Name                                       		 | Try it |
|:----------------|:-------------------------------------------------|--------|
| BulkMerge 	  | CoalesceDestinationOnMergeUpdateExpression 		 | [Fiddle](https://dotnetfiddle.net/fJSdcB) |
| BulkUpdate 	  | CoalesceDestinationOnUpdateExpression  	   		 | [Fiddle](https://dotnetfiddle.net/1jfmno) |
| BulkSynchronize | CoalesceDestinationOnSynchronizeUpdateExpression | [Fiddle](https://dotnetfiddle.net/1jfmno) |

## CoalesceDestinationOn[Action]Names

Use this option if you prefer to specify a list of properties names you want to include. The value must correspond to the property name or the navigation name.

```csharp
context.BulkMerge(customers, options => 
{
	// USE the code as the key expression
	options.ColumnPrimaryKeyExpression = x => x.Code;
	
	// ON UPDATE, modifiy the "Name" and "Email" column only if the destination value is null 
	options.CoalesceDestinationOnMergeUpdateNames = new List<string>() { nameof(Customer.Name), nameof(Customer.Email) };
});
```

| Method 		  | Name                                       		 | Try it |
|:----------------|:-------------------------------------------------|--------|
| BulkMerge 	  | CoalesceDestinationOnMergeUpdateNames 			 | [Fiddle](https://dotnetfiddle.net/AppZ13) |
| BulkUpdate 	  | CoalesceDestinationOnUpdateNames	   			 | [Fiddle](https://dotnetfiddle.net/rBhOY1) |
| BulkSynchronize | CoalesceDestinationOnSynchronizeUpdateNames	   	 | [Fiddle](https://dotnetfiddle.net/rBhOY1) |

## Related Solutions

- [Coalesce](doc-v2/coalesce.md)