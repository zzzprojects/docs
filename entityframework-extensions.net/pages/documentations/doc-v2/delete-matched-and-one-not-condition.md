# Delete Matched and One Not Condition

## Description

The `DeleteMatchedAndOneNotCondition` option lets you perform or skip the delete action, depending on if at least one value from the source is different than the destination for properties specified.

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

A company uses Entity Framework and needs to delete customers from an history table with the `BulkDelete` method.

However, there is a particularity. The delete should only happen if the history table is not the latest version.

In summary:

- If the `Version` value is the same, the customer cannot be deleted from the history table
- If the `Version` value is different, the customer can be deleted from the history table

## Solution

The`DeleteMatchedAndOneNotCondition` option have 4 solutions to this problem:

- [DeleteMatchedAndOneNotConditionExpression](#deletematchedandonenotconditionexpression)
- [DeleteMatchedAndOneNotConditionNames](#deletematchedandonenotconditionnames)
- [IgnoreOnDeleteMatchedAndOneNotConditionExpression](#ignoreondeletematchedandonenotconditionexpression)
- [IgnoreOnDeleteMatchedAndOneNotConditionNames](#ignoreondeletematchedandonenotconditionnames)

## DeleteMatchedAndOneNotConditionExpression

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
| BulkDelete	  | DeleteMatchedAndOneNotConditionExpression| [Fiddle](https://dotnetfiddle.net/uci5RT) |

## DeleteMatchedAndOneNotConditionNames

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
| BulkDelete 	  | DeleteMatchedAndOneNotConditionNames		  	 | [Fiddle](https://dotnetfiddle.net/U7t1PU) |

## IgnoreOnDeleteMatchedAndOneNotConditionExpression

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
| BulkDelete 	  | IgnoreOnDeleteMatchedAndOneNotConditionExpression| [Fiddle](https://dotnetfiddle.net/67SGs7) |

## IgnoreOnDeleteMatchedAndOneNotConditionNames

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
| BulkDelete 	  | IgnoreOnDeleteMatchedAndOneNotConditionNames 	 | [Fiddle](https://dotnetfiddle.net/WdSS7H) |


## Related Solutions

- [Delete Matched and Condition](doc-v2/delete-matched-and-condition.md)
- [Delete matched and Formula](doc-v2/delete-matched-and-formula.md)