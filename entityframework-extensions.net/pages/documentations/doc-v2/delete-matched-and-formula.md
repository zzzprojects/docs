# Delete Matched and Formula

## Description

The `DeleteMatchedAndFormula` option lets you perform or skip the update action, depending on the hardcoded SQL specified.

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

However, there is a particularity. The customer has a column `IsLocked` in the database:

- When `IsLocked = 0`, the customer can be updated
- When `IsLocked = 1`, the customer is locked and should not be updated

All customers to import have the value `IsLocked = true; // 0`, so the update action should only be performed when both `IsLocked` value (source and destination) are equals.

**Note**: We cannot use the `PrimaryKey` option in this scenario. Otherwise, when performing a `BulkMerge`, it will consider the locked customer as a new customer instead of an existing one and will insert it.

## Solution

The`MatchedAndCondition` option have 4 solutions to this problem:

- [DeleteMatchedAndFormula](#actionmatchedandconditionexpression)

## DeleteMatchedAndFormula

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
| BulkDelete 	  | DeleteMatchedAndFormula 		 | [Fiddle](https://dotnetfiddle.net/uci5RT) |

## Related Solutions

- [Matched and one not (Coming Soon)](#coming-soon)
- [MatchedAndFormula (Coming Soon)](#coming-soon)