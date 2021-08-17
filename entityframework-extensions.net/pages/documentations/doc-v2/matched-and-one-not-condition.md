# Matched and one not Condition

## Description

The `MatchedAndOneNotCondition` option lets you perform or skip the update action, depending on if at least one value for the source is different than the destination for properties specified.

### Example

```csharp
context.BulkMerge(customers, options => 
{
	// USE the code as the key expression
	options.ColumnPrimaryKeyExpression = x => x.Code;
	
	// ON UPDATE, modify customers only if a values such for the Code or Name has been modified
	options.MergeMatchedAndOneNotConditionExpression = x => new { x.Code, x.Name };
});
```

## Scenario

A company uses Entity Framework and needs to import customers with the `BulkMerge` method to insert new customers and update existing customers.

However, there is a particularity. The `Note` column is not really important and should not perform an update action if this is the only value that has been modified.

The update action should only be performed if another values such as the `Code` or `Name` has been modified.

In summary:

- If only the `Note` value is different, we don't want to update the customer.
- If any value specified in the `MatchedAndOneNotCondition` option is different, we want to update the customer

## Solution

The`MatchedAndOneNotCondition` option have 4 solutions to this problem:

- [[Action]MatchedAndOneNotConditionExpression](#actionmatchedandonenotconditionexpression)
- [[Action]MatchedAndOneNotConditionNames](#actionmatchedandonenotconditionnames)
- [IgnoreOn[Action]MatchedAndOneNotConditionExpression](#ignoreonactionmatchedandonenotconditionexpression)
- [IgnoreOn[Action]MatchedAndOneNotConditionNames](#ignoreonactionmatchedandonenotconditionnames)

## [Action]MatchedAnd

Use this option if you prefer to specify with an expression which properties you want to include.

```csharp
context.BulkMerge(customers, options => 
{
	// USE the code as the key expression
	options.ColumnPrimaryKeyExpression = x => x.Code;
	
	// ON UPDATE, modify customers only if a values such for the Code or Name has been modified
	options.MergeMatchedAndOneNotConditionExpression = x => new { x.Code, x.Name };
});
```

| Method 		  | Name                                     	   | Try it |
|:----------------|:-----------------------------------------------|--------|
| BulkMerge 	  | MergeMatchedAndOneNotConditionExpression 	   | [Fiddle](https://dotnetfiddle.net/LQZuak) |
| BulkUpdate 	  | UpdateMatchedAndOneNotConditionExpression	   | [Fiddle](https://dotnetfiddle.net/noelqT) |
| BulkSynchronize | SynchronizeMatchedAndOneNotConditionExpression | [Fiddle](https://dotnetfiddle.net/F7nbwA) |

## [Action]MatchedAndConditionNames

Use this option if you prefer to specify a list of properties names you want to include. The value must correspond to the property name or the navigation name.

```csharp
context.BulkMerge(customers, options => 
{
	// USE the code as the key expression
	options.ColumnPrimaryKeyExpression = x => x.Code;
	
	// ON UPDATE, modify customers only if a values such for the Code or Name has been modified
	options.MergeMatchedAndOneNotConditionNames = new List<string>() { nameof(Customer.Code), nameof(Customer.Name) };
});
```

| Method 		  | Name                                      | Try it |
|:----------------|:------------------------------------------|--------|
| BulkMerge 	  | MergeMatchedAndOneNotConditionNames		  | [Fiddle](https://dotnetfiddle.net/GFjZI3) |
| BulkUpdate 	  | UpdateMatchedAndOneNotConditionNames  	  | [Fiddle](https://dotnetfiddle.net/lq50C0) |
| BulkSynchronize | SynchronizeMatchedAndOneNotConditionNames | [Fiddle](https://dotnetfiddle.net/YYN2uZ) |

## IgnoreOn[Action]MatchedAndConditionExpression

Use this option if you prefer to specify with an expression which properties you want to exclude/ignore. All non-specified properties will be included.

```csharp
context.BulkMerge(customers, options => 
{
	// USE the code as the key expression
	options.ColumnPrimaryKeyExpression = x => x.Code;
	
	// ON UPDATE, modify customers only if a values such for the Code or Name has been modified by excluding all other properties
	options.IgnoreOnMergeMatchedAndOneNotConditionExpression = x => new { x.Note };
});
```

| Method 		  | Name                                       		 	   | Try it |
|:----------------|:-------------------------------------------------------|--------|
| BulkMerge 	  | IgnoreOnMergeMatchedAndOneNotConditionExpression 	   | [Fiddle](https://dotnetfiddle.net/XqgHKo) |
| BulkUpdate 	  | IgnoreOnUpdateMatchedAndOneNotConditionExpression  	   | [Fiddle](https://dotnetfiddle.net/65T8kP) |
| BulkSynchronize | IgnoreOnSynchronizeMatchedAndOneNotConditionExpression | [Fiddle](https://dotnetfiddle.net/zGSrJR) |

## IgnoreOn[Action]MatchedAndConditionNames

Use this option if you prefer to specify a list of properties names you want to exclude/ignore. The value must correspond to the property name or the navigation name. All non-specified properties will be included.

```csharp
context.BulkMerge(customers, options => 
{
	// USE the code as the key expression
	options.ColumnPrimaryKeyExpression = x => x.Code;
	
	// ON UPDATE, modify customers only if a values such for the Code or Name has been modified by excluding all other properties
	options.IgnoreOnMergeMatchedAndOneNotConditionNames = new List<string>() { nameof(Customer.Note) };
});
```

| Method 		  | Name                                       		  | Try it |
|:----------------|:--------------------------------------------------|--------|
| BulkMerge 	  | IgnoreOnMergeMatchedAndOneNotConditionNames		  | [Fiddle](https://dotnetfiddle.net/aSiWOu) |
| BulkUpdate 	  | IgnoreOnUpdateMatchedAndOneNotConditionNames  	  | [Fiddle](https://dotnetfiddle.net/4FZyj5) |
| BulkSynchronize | IgnoreOnSynchronizeMatchedAndOneNotConditionNames | [Fiddle](https://dotnetfiddle.net/03ayTe) |


## Related Solutions

- [Matched and Condition](doc-v2/matched-and-condition.md)
- [Matched and formula](doc-v2/matched-and-formula.md)