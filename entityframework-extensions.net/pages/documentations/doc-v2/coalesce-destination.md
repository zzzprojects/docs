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

However, there is a particularity. The customer should keep the current `Name` or `Email` values in the database if already specified.

- When `Destination.Email IS NULL`, the destination value should be updated
- When `Destination.Email IS NOT NULL`, the destination value should be keep

## Solution

The`CoalesceDestination` option have 2 solutions to this problem:

- [CoalesceDestinationOn[Action]Expression](#coalescedestinationonactionexpression)
- [CoalesceDestinationOn[Action]Names](#coalescedestinationonactionnames)

## [Action]MatchedAndConditionExpression

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

| Method 		  | Name                                       | Try it |
|:----------------|:-------------------------------------------|--------|
| BulkMerge 	  | CoalesceDestinationOnMergeUpdateExpression | [Fiddle](https://dotnetfiddle.net/fJSdcB) |
| BulkUpdate 	  | CoalesceDestinationOnUpdateExpression  	   | [Fiddle](https://dotnetfiddle.net/1jfmno) |

## [Action]MatchedAndConditionNames

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

## Related Solutions

- [Coalesce](doc-v2/coalesce.md)