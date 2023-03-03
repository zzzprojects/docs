---
Name: Delete Matched and Formula
LastMod: 2023-02-28
---

# Delete Matched and Formula

## Description

The `DeleteMatchedAndFormula` option lets you perform or skip the delete action, depending on the hardcoded SQL specified.

### Example

```csharp
context.BulkDelete(customers, options => 
{
	// REQUIRED because by default, only the "CustomerID" is part of the "StagingTable"
	options.ColumnStagingTableFormulaExpression = x => x.Version;
	
	// ON DELETE, remove customer where the version is equal or lower than the one coming from the importation
	options.DeleteMatchedAndFormula = "StagingTable.Version > DestinationTable.Version";
});
```

## Scenario

A company uses Entity Framework and needs to delete customers with the `BulkDelete` method.

However, there is a particularity. The delete should only happen if the version in the database is equal or lower as the one coming from the importation.

In summary:

- If the destination `Version` value is equal or lower, the customer can be deleted
- If the destination `Version` value is higher, the customer cannot be deleted

## Solution

The`DeleteMatchedAndFormula` option have 1 solutions to this problem:

- [DeleteMatchedAndFormula](#deletematchedandformula)

## DeleteMatchedAndFormula

Use this option to hardcode an SQL that returns a boolean. If the predicate is true, the delete action will be performed.

```csharp
context.BulkDelete(customers, options => 
{
	// REQUIRED because by default, only the "CustomerID" is part of the "StagingTable"
	options.ColumnStagingTableFormulaExpression = x => x.Version;
	
	// ON DELETE, remove customer where the version is equal or lower than the one coming from the importation
	options.DeleteMatchedAndFormula = "StagingTable.Version > DestinationTable.Version";
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