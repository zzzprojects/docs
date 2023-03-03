---
Name: Matched and Condition
LastMod: 2023-02-28
---

# Matched and Condition

<iframe width="560" height="315" src="https://www.youtube.com/embed/O2k8QZ4RHdw" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

## Description

The `MatchedAndCondition` option lets you perform or skip the update action, depending on if all values from the source and destination are equals for properties specified.

### Example

```csharp
context.BulkMerge(customers, options => 
{
	// ON UPDATE, modify customers that has the same `IsLocked` value (always "false" on the source)
	options.MergeMatchedAndConditionExpression = x => new { x.IsLocked };
});

```

## Scenario

A company uses Entity Framework and needs to import customers with the `BulkMerge` method to insert new customers and update existing customers.

However, there is a particularity. The customer has a column `IsLocked` in the database and any customer locked should not be updated.

- When `IsLocked = 0`, the customer can be updated
- When `IsLocked = 1`, the customer cannot be updated

All customers to import have the value `IsLocked = true; // 0`, so the update action should only be performed when both `IsLocked` values (source and destination) are equals.

**Note**: We cannot use the `PrimaryKey` option in this scenario. Otherwise, when performing a `BulkMerge`, it will consider the locked customer as a new customer instead of an existing one and insert it.

## Solution

The`MatchedAndCondition` option has 4 solutions to this problem:

- [[Action]MatchedAndConditionExpression](#actionmatchedandconditionexpression)
- [[Action]MatchedAndConditionNames](#actionmatchedandconditionnames)
- [IgnoreOn[Action]MatchedAndConditionExpression](#ignoreonactionmatchedandconditionexpression)
- [IgnoreOn[Action]MatchedAndConditionNames](#ignoreonactionmatchedandconditionnames)

## [Action]MatchedAndConditionExpression

Use this option if you prefer to specify with an expression which properties you want to include.

```csharp
context.BulkMerge(customers, options => 
{
	// ON UPDATE, modify customers that has the same `IsLocked` value (always "false" on the source)
	options.MergeMatchedAndConditionExpression = x => new { x.IsLocked };
});
```

| Method 		  | Name                                     | Try it |
|:----------------|:-----------------------------------------|--------|
| BulkMerge 	  | MergeMatchedAndConditionExpression 		 | [Fiddle](https://dotnetfiddle.net/uci5RT) |
| BulkUpdate 	  | UpdateMatchedAndConditionExpression  	 | [Fiddle](https://dotnetfiddle.net/NUwq90) |
| BulkSynchronize | SynchronizeMatchedAndConditionExpression | [Fiddle](https://dotnetfiddle.net/yFY5tG) |

## [Action]MatchedAndConditionNames

Use this option if you prefer to specify a list of property names you want to include. The value must correspond to the property name or the navigation name.

```csharp
context.BulkMerge(customers, options => 
{
	// ON UPDATE, modify customers that has the same `IsLocked` value (always "false" on the source)
	options.MergeMatchedAndConditionNames = new List<string>() { nameof(Customer.IsLocked) };
});
```

| Method 		  | Name                                       		 | Try it |
|:----------------|:-------------------------------------------------|--------|
| BulkMerge 	  | MergeMatchedAndConditionNames		 		 	 | [Fiddle](https://dotnetfiddle.net/U7t1PU) |
| BulkUpdate 	  | UpdateMatchedAndConditionNames  		 		 | [Fiddle](https://dotnetfiddle.net/ulHCGM) |
| BulkSynchronize | SynchronizeMatchedAndConditionNames	 		 	 | [Fiddle](https://dotnetfiddle.net/KiNuqb) |

## IgnoreOn[Action]MatchedAndConditionExpression

Use this option if you prefer to specify with an expression which properties you want to exclude/ignore. All non-specified properties will be included.

```csharp
context.BulkMerge(customers, options => 
{
	// ON UPDATE, modify customers that has the same `IsLocked` value (always "false" on the source) (by excluding other properties)
	options.IgnoreOnMergeMatchedAndConditionExpression = x => new { x.CustomerID, x.Name, x.Email, x.Note };
});
```

| Method 		  | Name                                       		 | Try it |
|:----------------|:-------------------------------------------------|--------|
| BulkMerge 	  | IgnoreOnMergeMatchedAndConditionExpression 		 | [Fiddle](https://dotnetfiddle.net/67SGs7) |
| BulkUpdate 	  | IgnoreOnUpdateMatchedAndConditionExpression  	 | [Fiddle](https://dotnetfiddle.net/PXlcOi) |
| BulkSynchronize | IgnoreOnSynchronizeMatchedAndConditionExpression | [Fiddle](https://dotnetfiddle.net/Zi6dzI) |

## IgnoreOn[Action]MatchedAndConditionNames

Use this option if you prefer to specify a list of property names you want to exclude/ignore. The value must correspond to the property name or the navigation name. All non-specified properties will be included.

```csharp
context.BulkMerge(customers, options => 
{
	// ON UPDATE, modify customers that has the same `IsLocked` value (always "false" on the source) (by excluding other properties)
	options.IgnoreOnMergeMatchedAndConditionNames = new List<string>() { nameof(Customer.CustomerID), nameof(Customer.Name), nameof(Customer.Email), nameof(Customer.Note) };
});
```

| Method 		  | Name                                       		 | Try it |
|:----------------|:-------------------------------------------------|--------|
| BulkMerge 	  | IgnoreOnMergeMatchedAndConditionNames 			 | [Fiddle](https://dotnetfiddle.net/WdSS7H) |
| BulkUpdate 	  | IgnoreOnUpdateMatchedAndConditionNames  		 | [Fiddle](https://dotnetfiddle.net/8Y4gis) |
| BulkSynchronize | IgnoreOnSynchronizeMatchedAndConditionNames		 | [Fiddle](https://dotnetfiddle.net/ippun6) |


## Related Solutions

- [Matched and one NOT Condition](doc-v2/matched-and-one-not-condition.md)
- [Matched and formula](doc-v2/matched-and-formula.md)
