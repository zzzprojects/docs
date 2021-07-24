# Matched and Condition

## Description

The `MatchedAndCondition` option lets you perform an update or not, depending on if properties specified in the condition have the same value as the one in the database.

## Scenario ( // or Problem Typical)

Our company manages multiple stores, and each store has its own "offline" database in case of a network failure (for example, when the internet is down, the store still needs to work).

Every night, a job runs to update customers in the centralized database and assign which store owns the customer's latest information.

During the day, all stores push modifications to the centralized database. However, we need to support partial updates and update the customer, but only if he is coming from the same store as the one assigned by the centralized database.

For example:

- The customer "Jonathan" has store "1" value as the latest store in the centralized database
- "Jonathan" has been modified and modification push to the centralized database
- If the modification comes from store "1", we want to update the centralized database
- If the modification comes from store "999", we want to skip the update (the night job will choose which store contains the latest information at the end of the day)

**Note**: We cannot use the "PrimaryKeyExpression" option since we use the BulkMerge method, and it will make the customer "Jonathan" considered a new customer. We want to be able to perform or not the update depending on a condition, the store in our example.

## Solution

You are currently looking for the **MatchedAndCondition** option.

We offer 4 solutions to this problem:

- [[Action]MatchedAndConditionExpression](#)
- [[Action]MatchedAndConditionNames](#)
- [IgnoreOn[Action]MatchedAndConditionExpression](#)
- [IgnoreOn[Action]MatchedAndConditionNames](#)

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

Use this option if you prefer to specify with a list of properties names you want to include. The value must correspond to the property name or the navigation name.

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

Use this option if you prefer to specify with a list of properties names you want to exclude/ignore. The value must correspond to the property name or the navigation name.

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


# Similar solutions
- [Matched and one not (Incomming)](#incomming)
- [MatchedAndFormula (Incomming)](#incomming)
