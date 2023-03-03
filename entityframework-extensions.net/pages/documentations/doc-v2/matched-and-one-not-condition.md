---
Name: Matched and one NOT Condition
LastMod: 2023-02-28
---

# Matched and one NOT Condition

<iframe width="560" height="315" src="https://www.youtube.com/embed/jCgnIVF0g-8" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

## Description

The `MatchedAndOneNotCondition` option lets you perform or skip the update action, depending on if at least one value from the source is different than the destination for properties specified.

### Example

```csharp
context.BulkMerge(customers, options => 
{
	// ON UPDATE, modify the customer if a values for "Name" or "Email" is different
	options.MergeMatchedAndOneNotConditionExpression = x => new { x.Name, x.Email };
});

```

## Scenario

A company uses Entity Framework and needs to import customers with the `BulkMerge` method to insert new customers and update existing customers.

However, there is a particularity. The `Note` column is not really important and should not trigger an update action if this is the only value that has been modified.

The update action should only be performed if another values such as the `Name` or `Email` has been modified.

In summary:

- If the `Name` or `Email` value is different to the database, the customer can be updated
- If the `Name` or `Email` value is equal to the database, the customer cannot be updated

## Solution

The`MatchedAndOneNotCondition` option have 4 solutions to this problem:

- [[Action]MatchedAndOneNotConditionExpression](#actionmatchedandonenotconditionexpression)
- [[Action]MatchedAndOneNotConditionNames](#actionmatchedandonenotconditionnames)
- [IgnoreOn[Action]MatchedAndOneNotConditionExpression](#ignoreonactionmatchedandonenotconditionexpression)
- [IgnoreOn[Action]MatchedAndOneNotConditionNames](#ignoreonactionmatchedandonenotconditionnames)

## [Action]MatchedAndOneNotConditionExpression

Use this option if you prefer to specify with an expression which properties you want to include.

```csharp
context.BulkMerge(customers, options => 
{
	// ON UPDATE, modify the customer if a values for "Name" or "Email" is different
	options.MergeMatchedAndOneNotConditionExpression = x => new { x.Name, x.Email };
});
```

| Method 		  | Name                                     	   | Try it |
|:----------------|:-----------------------------------------------|--------|
| BulkMerge 	  | MergeMatchedAndOneNotConditionExpression 	   | [Fiddle](https://dotnetfiddle.net/LQZuak) |
| BulkUpdate 	  | UpdateMatchedAndOneNotConditionExpression	   | [Fiddle](https://dotnetfiddle.net/noelqT) |
| BulkSynchronize | SynchronizeMatchedAndOneNotConditionExpression | [Fiddle](https://dotnetfiddle.net/F7nbwA) |

## [Action]MatchedAndOneNotConditionNames

Use this option if you prefer to specify a list of property names you want to include. The value must correspond to the property name or the navigation name.

```csharp
context.BulkMerge(customers, options => 
{
	// ON UPDATE, modify the customer if a values for "Name" or "Email" is different
	options.MergeMatchedAndOneNotConditionNames = new List<string>() { nameof(Customer.Name), nameof(Customer.Email) };
});
```

| Method 		  | Name                                      | Try it |
|:----------------|:------------------------------------------|--------|
| BulkMerge 	  | MergeMatchedAndOneNotConditionNames		  | [Fiddle](https://dotnetfiddle.net/GFjZI3) |
| BulkUpdate 	  | UpdateMatchedAndOneNotConditionNames  	  | [Fiddle](https://dotnetfiddle.net/lq50C0) |
| BulkSynchronize | SynchronizeMatchedAndOneNotConditionNames | [Fiddle](https://dotnetfiddle.net/YYN2uZ) |

## IgnoreOn[Action]MatchedAndOneNotConditionExpression

Use this option if you prefer to specify with an expression which properties you want to exclude/ignore. All non-specified properties will be included.

```csharp
context.BulkMerge(customers, options => 
{
	// ON UPDATE, modify the customer if a values for "Name" or "Email" is different (by excluding other properties)
	options.IgnoreOnMergeMatchedAndOneNotConditionExpression = x => new { x.Note };
});
```

| Method 		  | Name                                       		 	   | Try it |
|:----------------|:-------------------------------------------------------|--------|
| BulkMerge 	  | IgnoreOnMergeMatchedAndOneNotConditionExpression 	   | [Fiddle](https://dotnetfiddle.net/XqgHKo) |
| BulkUpdate 	  | IgnoreOnUpdateMatchedAndOneNotConditionExpression  	   | [Fiddle](https://dotnetfiddle.net/65T8kP) |
| BulkSynchronize | IgnoreOnSynchronizeMatchedAndOneNotConditionExpression | [Fiddle](https://dotnetfiddle.net/zGSrJR) |

## IgnoreOn[Action]MatchedAndOneNotConditionNames

Use this option if you prefer to specify a list of property names you want to exclude/ignore. The value must correspond to the property name or the navigation name. All non-specified properties will be included.

```csharp
context.BulkMerge(customers, options => 
{
	// ON UPDATE, modify the customer if a values for "Name" or "Email" is different (by excluding other properties)
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