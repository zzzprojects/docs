---
Name: Delete Matched and one NOT Condition
LastMod: 2023-02-28
---

# Delete Matched and one NOT Condition

## Description

The `DeleteMatchedAndOneNotCondition` option lets you perform or skip the delete action, depending on if at least one value from the source is different than the destination for properties specified.

### Example

```csharp
context.BulkDelete(customers, options => 
{
	// ON DELETE, remove customer where the version is not equal
	options.DeleteMatchedAndOneNotConditionExpression = x => new { x.Version };
});
```

## Scenario

A company uses Entity Framework and needs to delete customers with the `BulkDelete` method.

However, there is a particularity. The delete should only happen if the version in the database is not equal as the one coming from the importation.

In summary:

- If the `Version` value are equal, the customer cannot be deleted
- If the `Version` value are not equal, the customer can be deleted

## Solution

The`DeleteMatchedAndOneNotCondition` option have 4 solutions to this problem:

- [DeleteMatchedAndOneNotConditionExpression](#deletematchedandonenotconditionexpression)
- [DeleteMatchedAndOneNotConditionNames](#deletematchedandonenotconditionnames)
- [IgnoreOnDeleteMatchedAndOneNotConditionExpression](#ignoreondeletematchedandonenotconditionexpression)
- [IgnoreOnDeleteMatchedAndOneNotConditionNames](#ignoreondeletematchedandonenotconditionnames)

## DeleteMatchedAndOneNotConditionExpression

Use this option if you prefer to specify with an expression which properties you want to include.

```csharp
context.BulkDelete(customers, options => 
{
	// ON DELETE, remove customer where the version is not equal
	options.DeleteMatchedAndOneNotConditionExpression = x => new { x.Version };
});
```

| Method 		  | Name                                     | Try it |
|:----------------|:-----------------------------------------|--------|
| BulkDelete	  | DeleteMatchedAndOneNotConditionExpression| [Fiddle](https://dotnetfiddle.net/lETrFX) |

## DeleteMatchedAndOneNotConditionNames

Use this option if you prefer to specify a list of property names you want to include. The value must correspond to the property name or the navigation name.

```csharp
context.BulkDelete(customers, options => 
{
	// ON DELETE, remove customer where the version is not equal
	options.DeleteMatchedAndOneNotConditionNames = new List<string>() { nameof(Customer.Version) };
});
```

| Method 		  | Name                                       		 | Try it |
|:----------------|:-------------------------------------------------|--------|
| BulkDelete 	  | DeleteMatchedAndOneNotConditionNames		  	 | [Fiddle](https://dotnetfiddle.net/u3uEpb) |

## IgnoreOnDeleteMatchedAndOneNotConditionExpression

Use this option if you prefer to specify with an expression which properties you want to exclude/ignore. All non-specified properties will be included.

```csharp
context.BulkDelete(customers, options => 
{
	// ON DELETE, remove customer where the version is not equal (by excluding other properties)
	options.IgnoreOnDeleteMatchedAndOneNotConditionExpression  = x => new { x.CustomerID, x.Name, x.Email, x.Note };
});
```

| Method 		  | Name                                       		 | Try it |
|:----------------|:-------------------------------------------------|--------|
| BulkDelete 	  | IgnoreOnDeleteMatchedAndOneNotConditionExpression| [Fiddle](https://dotnetfiddle.net/bNOJXF) |

## IgnoreOnDeleteMatchedAndOneNotConditionNames

Use this option if you prefer to specify a list of property names you want to exclude/ignore. The value must correspond to the property name or the navigation name. All non-specified properties will be included.

```csharp
context.BulkDelete(customers, options => 
{
	// ON DELETE, remove customer where the version is not equal (by excluding other properties)
	options.IgnoreOnDeleteMatchedAndOneNotConditionNames = new List<string>() { nameof(Customer.CustomerID), nameof(Customer.Name), nameof(Customer.Email), nameof(Customer.Note) };
});
```

| Method 		  | Name                                       		 | Try it |
|:----------------|:-------------------------------------------------|--------|
| BulkDelete 	  | IgnoreOnDeleteMatchedAndOneNotConditionNames 	 | [Fiddle](https://dotnetfiddle.net/DFHMaU) |


## Related Solutions

- [Delete Matched and Condition](doc-v2/delete-matched-and-condition.md)
- [Delete matched and Formula](doc-v2/delete-matched-and-formula.md)