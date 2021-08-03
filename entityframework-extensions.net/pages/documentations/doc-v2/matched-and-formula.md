# Matched and Formula

## Description

The `MatchedAndFormula` option lets you perform or skip the update action, depending on the hardcoded SQL specified.

### Example

```csharp
context.BulkMerge(customers, options => 
{
	// USE the code as the key expression
	options.ColumnPrimaryKeyExpression = x => x.Code;
	
	// ON UPDATE, modify customers only that has the same `IsLocked` value (always 0 on the source)
	options.MergeMatchedAndConditionExpression = x => new { x.IsLocked };
});
```

## Scenario

A company uses Entity Framework and needs to import customers with the `BulkMerge` method to insert new customers and update existing customers.

However, there is a particularity. We only want to update the customer if the `Version` value is higher in the source than in the database, otherwise we consider the database value having the latest customer information.

In summary:

- If the `Version` value of the source is greater than the value in the database, the customer can be updated
- If the `Version` value of the source is lower or equal than the value in the database, the customer should not be updated

## Solution

The`MatchedAndCondition` option have 4 solutions to this problem:

- [[Action]MatchedAndConditionExpression](#actionmatchedandconditionexpression)
- [[Action]MatchedAndConditionNames](#actionmatchedandconditionnames)
- [IgnoreOn[Action]MatchedAndConditionExpression](#ignoreonactionmatchedandconditionexpression)
- [IgnoreOn[Action]MatchedAndConditionNames](#ignoreonactionmatchedandconditionnames)

## [Action]MatchedAndFormula

Use this option if you prefer to specify with an expression which properties you want to include.

```csharp
context.BulkMerge(customers, options => 
{
	// USE the code as the key expression
	options.ColumnPrimaryKeyExpression = x => x.Code;
	
	// ON UPDATE, modify customers only that has the same `IsLocked` value (always 0 on the source)
	options.MergeMatchedAndConditionExpression = x => new { x.IsLocked };
});
```

| Method 		  | Name                                     | Try it |
|:----------------|:-----------------------------------------|--------|
| BulkMerge 	  | MergeMatchedAndFormula 		 | [Fiddle](#) |
| BulkUpdate 	  | UpdateMatchedAndFormula  	 | [Fiddle](#) |
| BulkSynchronize | SynchronizeMatchedAndFormula | [Fiddle](#) |

## Related Solutions

- [Matched and one not (Coming Soon)](#coming-soon)
- [MatchedAndFormula (Coming Soon)](#coming-soon)