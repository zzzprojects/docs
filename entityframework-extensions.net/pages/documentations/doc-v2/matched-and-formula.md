# Matched and Formula

## Description

The `MatchedAndFormula` option lets you perform or skip the update action, depending on the hardcoded SQL specified.

### Example

```csharp
context.BulkMerge(customers, options => 
{
	// USE the code as the key expression
	options.ColumnPrimaryKeyExpression = x => x.Code;
	
	// ON UPDATE, modify customers that have a lower version in the database than in the source
	// StagingTable = source
	// DestinationTable = database/destination
	options.MergeMatchedAndFormula = "StagingTable.Version > DestinationTable.Version";
});
```

## Scenario

A company uses Entity Framework and needs to import customers with the `BulkMerge` method to insert new customers and update existing customers.

However, there is a particularity. We only want to update the customer if the `Version` value is higher in the source than in the database. Otherwise, we consider the database value having the latest customer information.

In summary:

- If the `Version` value of the source is greater than the value in the database, the customer can be updated
- If the `Version` value of the source is lower or equal then the value in the database, the customer should not be updated

## Solution

The`MatchedAndFormula` have 1 solution to this problem:

- [[Action]MatchedAndFormula](#actionmatchedandformula)

## [Action]MatchedAndFormula

Use this option to hardcode an SQL that returns a boolean. If the predicate is true, the update action will be performed.

Alias the formula:

- StagingTable: The staging table contains data from your datasource/entities
- DestinationTable: The destination table is your table in the database

```csharp
context.BulkMerge(customers, options => 
{
	// USE the code as the key expression
	options.ColumnPrimaryKeyExpression = x => x.Code;
	
	// ON UPDATE, modify customers that have a lower version in the database than in the source
	// StagingTable = source
	// DestinationTable = database/destination
	options.MergeMatchedAndFormula = "StagingTable.Version > DestinationTable.Version";
});
```

| Method 		  | Name                                     | Try it |
|:----------------|:-----------------------------------------|--------|
| BulkMerge 	  | MergeMatchedAndFormula 		 | [Fiddle](https://dotnetfiddle.net/sgEyDR) |
| BulkUpdate 	  | UpdateMatchedAndFormula  	 | [Fiddle](https://dotnetfiddle.net/cTGNTh) |
| BulkSynchronize | SynchronizeMatchedAndFormula | [Fiddle](https://dotnetfiddle.net/zsroHG) |

## Related Solutions

- [Matched and Condition](doc-v2/matched-and-condition.md)
- [Matched and one NOT Condition](doc-v2/matched-and-one-not-condition.md)