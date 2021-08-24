# Delete Matched and Condition

## Description

The `DeleteMatchedAndCondition` option lets you perform or skip the delete action, depending on if all values from the source and destination are equals for properties specified.

### Example

```csharp
context.BulkDelete(customers, options => 
{
	// USE the code as the key expression
	options.ColumnPrimaryKeyExpression = x => x.Code;
	
	// ON DELETE, remove customer that has the same version only
	options.DeleteMatchedAndConditionExpression = x => new { x.Version };
});
```

## Scenario

A company uses Entity Framework and needs to delete customers with the `BulkDelete` method.

However, there is a particularity. The delete should only happen if the version in the database is the same as the one coming from the importation.

In summary:

- If the `Version` value is the same, the customer can be deleted
- If the `Version` value is different, the customer cannot be deleted

## Solution

The`MatchedAndCondition` option have 4 solutions to this problem:

- [DeleteMatchedAndConditionExpression](#actionmatchedandconditionexpression)
- [DeleteMatchedAndConditionNames](#actionmatchedandconditionnames)
- [IgnoreOnDeleteMatchedAndConditionExpression](#ignoreonactionmatchedandconditionexpression)
- [IgnoreOnDeleteMatchedAndConditionNames](#ignoreonactionmatchedandconditionnames)

## DeleteMatchedAndConditionExpression

Use this option if you prefer to specify with an expression which properties you want to include.

```csharp
context.BulkDelete(customers, options => 
{
	// USE the code as the key expression
	options.ColumnPrimaryKeyExpression = x => x.Code;
	
	// ON DELETE, remove customer that has the same version only
	options.DeleteMatchedAndConditionExpression = x => new { x.Version };
});
```

| Method 		  | Name                                     | Try it |
|:----------------|:-----------------------------------------|--------|
| BulkDelete 	  | DeleteMatchedAndConditionExpression 	 | [Fiddle](https://dotnetfiddle.net/kEVym8) |

## [Action]MatchedAndConditionNames

Use this option if you prefer to specify a list of properties names you want to include. The value must correspond to the property name or the navigation name.

```csharp
context.BulkDelete(customers, options => 
{
	// USE the code as the key expression
	options.ColumnPrimaryKeyExpression = x => x.Code;
	
	// ON DELETE, remove customer that has the same version only
	options.DeleteMatchedAndConditionNames = new List<string>() { nameof(Customer.Name) };
});
```

| Method 		  | Name                                       		 | Try it |
|:----------------|:-------------------------------------------------|--------|
| BulkDelete 	  | DeleteMatchedAndConditionNames		 		 	 | [Fiddle](https://dotnetfiddle.net/wVRpZR) |

## IgnoreOnDeleteMatchedAndConditionExpression

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

## IgnoreOnDeleteMatchedAndConditionNames

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

- [Delete Matched and one NOT Condition](doc-v2/delete-matched-and-one-not-condition.md)
- [Delete matched and Formula](doc-v2/delete-matched-and-formula.md)