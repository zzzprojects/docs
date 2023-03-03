---
Name: Delete Matched and Condition
LastMod: 2023-02-28
---

# Delete Matched and Condition

## Description

The `DeleteMatchedAndCondition` option lets you perform or skip the delete action, depending on if all values from the source and destination are equals for properties specified.

### Example

```csharp
context.BulkDelete(customers, options => 
{
	// ON DELETE, remove customer where the version is equal
	options.DeleteMatchedAndConditionExpression = x => new { x.Version };
});
```

## Scenario

A company uses Entity Framework and needs to delete customers with the `BulkDelete` method.

However, there is a particularity. The delete should only happen if the version in the database is equal as the one coming from the importation.

In summary:

- If the `Version` value are equal, the customer can be deleted
- If the `Version` value are not equal, the customer cannot be deleted

## Solution

The`DeleteMatchedAndCondition` option have 4 solutions to this problem:

- [DeleteMatchedAndConditionExpression](#deletematchedandconditionexpression)
- [DeleteMatchedAndConditionNames](#deletematchedandconditionnames)
- [IgnoreOnDeleteMatchedAndConditionExpression](#ignoreondeletematchedandconditionexpression)
- [IgnoreOnDeleteMatchedAndConditionNames](#ignoreondeletematchedandconditionnames)

## DeleteMatchedAndConditionExpression

Use this option if you prefer to specify with an expression which properties you want to include.

```csharp
context.BulkDelete(customers, options => 
{
	// ON DELETE, remove customer where the version is equal
	options.DeleteMatchedAndConditionExpression = x => new { x.Version };
});
```

| Method 		  | Name                                     | Try it |
|:----------------|:-----------------------------------------|--------|
| BulkDelete 	  | DeleteMatchedAndConditionExpression 	 | [Fiddle](https://dotnetfiddle.net/kEVym8) |

## DeleteMatchedAndConditionNames

Use this option if you prefer to specify a list of property names you want to include. The value must correspond to the property name or the navigation name.

```csharp
context.BulkDelete(customers, options => 
{
	// ON DELETE, remove customer where the version is equal
	options.DeleteMatchedAndConditionNames = new List<string>() { nameof(Customer.Version) };
});
```

| Method 		  | Name                                       		 | Try it |
|:----------------|:-------------------------------------------------|--------|
| BulkDelete 	  | DeleteMatchedAndConditionNames		 		 	 | [Fiddle](https://dotnetfiddle.net/wVRpZR) |

## IgnoreOnDeleteMatchedAndConditionExpression

Use this option if you prefer to specify with an expression which properties you want to exclude/ignore. All non-specified properties will be included.

```csharp
context.BulkDelete(customers, options => 
{
	// ON DELETE, remove customer where the version is equal (by excluding other properties)
	options.IgnoreOnDeleteMatchedAndConditionExpression  = x => new { x.CustomerID, x.Name, x.Email, x.Note };
});
```

| Method 		  | Name                                       		 | Try it |
|:----------------|:-------------------------------------------------|--------|
| BulkDelete 	  | IgnoreOnDeleteMatchedAndConditionExpression 	 | [Fiddle](https://dotnetfiddle.net/b3oNfG) |

## IgnoreOnDeleteMatchedAndConditionNames

Use this option if you prefer to specify a list of property names you want to exclude/ignore. The value must correspond to the property name or the navigation name. All non-specified properties will be included.

```csharp
context.BulkDelete(customers, options => 
{
	// ON DELETE, remove customer where the version is equal (by excluding other properties)
	options.IgnoreOnDeleteMatchedAndConditionNames = new List<string>() { nameof(Customer.CustomerID), nameof(Customer.Name), nameof(Customer.Email), nameof(Customer.Note) };
});
```

| Method 		  | Name                                       		 | Try it |
|:----------------|:-------------------------------------------------|--------|
| BulkDelete 	  | IgnoreOnDeleteMatchedAndConditionNames 			 | [Fiddle](https://dotnetfiddle.net/D2BciE) |


## Related Solutions

- [Delete Matched and one NOT Condition](doc-v2/delete-matched-and-one-not-condition.md)
- [Delete matched and Formula](doc-v2/delete-matched-and-formula.md)