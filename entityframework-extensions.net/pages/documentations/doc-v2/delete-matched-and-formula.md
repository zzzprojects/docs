# Delete Matched and Formula

## Description

The `DeleteMatchedAndFormula` option lets you perform or skip the delete action, depending on the hardcoded SQL specified.

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

A company uses Entity Framework and needs to delete customers with the `BulkDelete` method.

However, there is a particularity. The delete should only happen if the version in the database is the same as the one coming from the importation.

In summary:

- If the `Version` value is the same, the customer can be deleted
- If the `Version` value is different, the customer cannot be deleted

## Solution

The`DeleteMatchedAndFormula` option have 1 solutions to this problem:

- [DeleteMatchedAndFormula](#deletematchedandformula)

## DeleteMatchedAndFormula

Use this option to hardcode an SQL that returns a boolean. If the predicate is true, the delete action will be performed.

```csharp
context.BulkMerge(customers, options => 
{
	// USE the code as the key expression
	options.ColumnPrimaryKeyExpression = x => x.Code;
	
	// ON UPDATE, modify customers only that has the same `IsLocked` value (always 0 on the source)
	options.MergeMatchedAndConditionExpression = x => new { x.IsLocked };
});
```

Table Alias:

- `DestinationTable`: Alias for the table in the database
- `StagingTable`: Alias for the table containing value from the source

| Method 		  | Name                                     | Try it |
|:----------------|:-----------------------------------------|--------|
| BulkDelete 	  | DeleteMatchedAndFormula 		 | [Fiddle](https://dotnetfiddle.net/mwjtvN) |

## Related Solutions

- [Delete Matched and Condition](doc-v2/delete-matched-and-condition.md)
- [Delete Matched and one NOT Condition](doc-v2/delete-matched-and-one-not-condition.md)