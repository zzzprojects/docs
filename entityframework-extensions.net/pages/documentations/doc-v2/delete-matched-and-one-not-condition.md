# Delete Matched and One Not Condition

## Description

The `DeleteMatchedAndOneNotCondition` option lets you perform or skip the update action, depending on if at least one value for the source is different than the destination for properties specified.

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

- [DeleteMatchedAndConditionExpression](#actionmatchedandconditionexpression)
- [DeleteMatchedAndConditionNames](#actionmatchedandconditionnames)
- [IgnoreOnDeleteMatchedAndConditionExpression](#ignoreonactionmatchedandconditionexpression)
- [IgnoreOnDeleteMatchedAndConditionNames](#ignoreonactionmatchedandconditionnames)

## [Action]MatchedAndConditionExpression

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
| BulkDelete	  | DeleteMatchedAndConditionExpression 		 | [Fiddle](https://dotnetfiddle.net/uci5RT) |

## [Action]MatchedAndConditionNames

Use this option if you prefer to specify a list of properties names you want to include. The value must correspond to the property name or the navigation name.

```csharp
context.BulkMerge(customers, options => 
{
	// USE the code as the key expression
	options.ColumnPrimaryKeyExpression = x => x.Code;
	
	// ON UPDATE, modify customers only that has the same `IsLocked` value (always 0 on the source)
	options.MergeMatchedAndConditionNames = new List<string>() { nameof(Customer.IsLocked) };
});
```

| Method 		  | Name                                       		 | Try it |
|:----------------|:-------------------------------------------------|--------|
| BulkDelete 	  | DeleteMatchedAndConditionNames		 		 	 | [Fiddle](https://dotnetfiddle.net/U7t1PU) |

## IgnoreOn[Action]MatchedAndConditionExpression

Use this option if you prefer to specify with an expression which properties you want to exclude/ignore. All non-specified properties will be included.

```csharp
context.BulkMerge(customers, options => 
{
	// USE the code as the key expression
	options.ColumnPrimaryKeyExpression = x => x.Code;
	
	// ON UPDATE, modify customers only that has the same `IsLocked` value by excluding all other properties (always 0 on the source)
	options.IgnoreOnMergeMatchedAndConditionExpression = x => new { x.CustomerID, x.Name, x.Description };
});
```

| Method 		  | Name                                       		 | Try it |
|:----------------|:-------------------------------------------------|--------|
| BulkDelete 	  | IgnoreOnDeleteMatchedAndConditionExpression 		 | [Fiddle](https://dotnetfiddle.net/67SGs7) |

## IgnoreOn[Action]MatchedAndConditionNames

Use this option if you prefer to specify a list of properties names you want to exclude/ignore. The value must correspond to the property name or the navigation name. All non-specified properties will be included.

```csharp
context.BulkMerge(customers, options => 
{
	// USE the code as the key expression
	options.ColumnPrimaryKeyExpression = x => x.Code;
	
	// ON UPDATE, modify customers only that has the same `IsLocked` value by excluding all other properties (always 0 on the source)
	options.IgnoreOnMergeMatchedAndConditionNames = new List<string>() { nameof(Customer.CustomerID), nameof(Customer.Name), nameof(Customer.Description) };
});
```

| Method 		  | Name                                       		 | Try it |
|:----------------|:-------------------------------------------------|--------|
| BulkDelete 	  | IgnoreOnDeleteMatchedAndConditionNames 			 | [Fiddle](https://dotnetfiddle.net/WdSS7H) |


## Related Solutions

- [Delete Matched and Condition](doc-v2/delete-matched-and-condition.md)
- [Delete matched and Formula](doc-v2/delete-matched-and-formula.md)