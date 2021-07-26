# Matched and Condition

## Description

The `MatchedAndCondition` option lets you perform or skip the update action, depending on if all values from the source and destination are equals for properties specified.

For people using SQL Server, this is the `<Condition_AllColumnsValuesEqual>` of the following MERGE statement:

```sql
MERGE <DestinationTable>
USING <StagingTable>
ON <PrimaryKey>
WHEN MATCHED AND <Condition_AllColumnsValuesEqual> THEN
	UPDATE SET <UpdateColumnNames>
WHEN NOT MATCHED THEN
	INSERT (<InsertColumnNames>)
	VALUES (<StagingTable.InsertColumnNames>)
```

## Scenario

A company manages multiple stores, and each store has its own "offline" database in case of a network failure (for example, when the internet is down, the store still needs to be fully operational).

Every night, a job runs to update customers in the centralized database and assign which store owns the customer's latest information.

During the day, all stores push modifications to the centralized database. However, the customer is updated in the centralized database only when the data is coming from the same assigned store.

For example:

- The customer "Jonathan" has store "1" value as the latest store in the centralized database
- "Jonathan" has been modified and the modification pushed to the centralized database
- If the modification comes from store "1", the customer "Jonathan" is updated in the centralized database
- If the modification comes from store "999", we want to skip the update and let the night job choose which store contains the latest information at the end of the day

**Note**: We cannot use the `PrimaryKey` option in this scenario. Otherwise, when performing a `BulkMerge`, it will consider "Jonathan" as a new customer and insert it.

## Solution

The`MatchedAndCondition` option have 4 solutions to this problem:

- [[Action]MatchedAndConditionExpression](#actionmatchedandconditionexpression)
- [[Action]MatchedAndConditionNames](#actionmatchedandconditionnames)
- [IgnoreOn[Action]MatchedAndConditionExpression](#ignoreonactionmatchedandconditionexpression)
- [IgnoreOn[Action]MatchedAndConditionNames](#ignoreonactionmatchedandconditionnames)

## [Action]MatchedAndConditionExpression

Use this option if you prefer to specify with an expression which properties you want to include.

```csharp
context.BulkMerge(customers, options => 
{
	// USE the code as the key expression
	options.ColumnPrimaryKeyExpression = x => x.Code;
	
	// UPDATE only customers coming from this store
	options.MergeMatchedAndConditionExpression = x => new { x.Store };
});
```

| Action 		  | Name                                     | Try it |
|:----------------|:-----------------------------------------|--------|
| BulkMerge 	  | MergeMatchedAndConditionExpression 		 | [Fiddle](https://dotnetfiddle.net/uci5RT) |
| BulkUpdate 	  | UpdateMatchedAndConditionExpression  	 | [Fiddle](https://dotnetfiddle.net/NUwq90) |
| BulkSynchronize | SynchronizeMatchedAndConditionExpression | [Fiddle](https://dotnetfiddle.net/yFY5tG) |

## [Action]MatchedAndConditionNames

Use this option if you prefer to specify a list of properties names you want to include. The value must correspond to the property name or the navigation name.

```csharp
context.BulkMerge(customers, options => 
{
	// USE the code as the key expression
	options.ColumnPrimaryKeyExpression = x => x.Code;
	
	// UPDATE only customers coming from this store
	options.MergeMatchedAndConditionNames = new List<string>() { nameof(Customer.Store) };
});
```

| Action 		  | Name                                       		 | Try it |
|:----------------|:-------------------------------------------------|--------|
| BulkMerge 	  | MergeMatchedAndConditionNames		 		 	 | [Fiddle](https://dotnetfiddle.net/U7t1PU) |
| BulkUpdate 	  | UpdateMatchedAndConditionNames  		 		 | [Fiddle](https://dotnetfiddle.net/ulHCGM) |
| BulkSynchronize | SynchronizeMatchedAndConditionNames	 		 	 | [Fiddle](https://dotnetfiddle.net/KiNuqb) |

## IgnoreOn[Action]MatchedAndConditionExpression

Use this option if you prefer to specify with an expression which properties you want to exclude/ignore.

```csharp
context.BulkMerge(customers, options => 
{
	// USE the code as the key expression
	options.ColumnPrimaryKeyExpression = x => x.Code;
	
	// UPDATE only customers coming from this store (by excluding all other properties)
	options.IgnoreOnMergeMatchedAndConditionExpression = x => new { x.CustomerID, x.Name, x.Description };
});
```

| Action 		  | Name                                       		 | Try it |
|:----------------|:-------------------------------------------------|--------|
| BulkMerge 	  | IgnoreOnMergeMatchedAndConditionExpression 		 | [Fiddle](https://dotnetfiddle.net/67SGs7) |
| BulkUpdate 	  | IgnoreOnUpdateMatchedAndConditionExpression  	 | [Fiddle](https://dotnetfiddle.net/PXlcOi) |
| BulkSynchronize | IgnoreOnSynchronizeMatchedAndConditionExpression | [Fiddle](https://dotnetfiddle.net/Zi6dzI) |

## IgnoreOn[Action]MatchedAndConditionNames

Use this option if you prefer to specify a list of properties names you want to exclude/ignore. The value must correspond to the property name or the navigation name.

```csharp
context.BulkMerge(customers, options => 
{
	// USE the code as the key expression
	options.ColumnPrimaryKeyExpression = x => x.Code;
	
	// UPDATE only customers coming from this store (by excluding all other properties)
	options.IgnoreOnMergeMatchedAndConditionNames = new List<string>() { nameof(Customer.CustomerID), nameof(Customer.Name), nameof(Customer.Description) };
});
```

| Action 		  | Name                                       		 | Try it |
|:----------------|:-------------------------------------------------|--------|
| BulkMerge 	  | IgnoreOnMergeMatchedAndConditionNames 			 | [Fiddle](https://dotnetfiddle.net/WdSS7H) |
| BulkUpdate 	  | IgnoreOnUpdateMatchedAndConditionNames  		 | [Fiddle](https://dotnetfiddle.net/8Y4gis) |
| BulkSynchronize | IgnoreOnSynchronizeMatchedAndConditionNames		 | [Fiddle](https://dotnetfiddle.net/ippun6) |


## Related Solutions

- [Matched and one not (Coming Soon)](#coming-soon)
- [MatchedAndFormula (Coming Soon)](#coming-soon)