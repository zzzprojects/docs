---
Name: Matched and Formula
LastMod: 2023-02-28
---

# Matched and Formula

<iframe width="560" height="315" src="https://www.youtube.com/embed/HiujI5LpEtE" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

## Description

The `MatchedAndFormula` option lets you perform or skip the update action, depending on the hardcoded SQL specified.

### Example

```csharp
context.BulkMerge(customers, options => 
{
	// ON UPDATE, modify customers where the version is equal or lower than the one coming from the importation
	// StagingTable = source
	// DestinationTable = database/destination
	options.MergeMatchedAndFormula = "StagingTable.Version > DestinationTable.Version";
});
```

## Scenario

A company uses Entity Framework and needs to import customers with the `BulkMerge` method to insert new customers and update existing customers.

However, there is a particularity. The customer should only be updated if the `Version` value is higher in the source than in the database. Otherwise, we consider the database value having the latest customer information.

In summary:

- If the source `Version` value is higher, the customer can be updated
- If the source `Version` value is equal or lower, the customer cannot be updated

## Solution

The`MatchedAndFormula` have 1 solution to this problem:

- [[Action]MatchedAndFormula](#actionmatchedandformula)

## [Action]MatchedAndFormula

Use this option to hardcode an SQL that returns a boolean. If the predicate is true, the update action will be performed.

```csharp
context.BulkMerge(customers, options => 
{
	// ON UPDATE, modify customers where the version is equal or lower than the one coming from the importation
	// StagingTable = source
	// DestinationTable = database/destination
	options.MergeMatchedAndFormula = "StagingTable.Version > DestinationTable.Version";
});
```

Table Alias:

- `DestinationTable`: Alias for the table in the database
- `StagingTable`: Alias for the table containing value from the source

| Method 		  | Name                                     | Try it |
|:----------------|:-----------------------------------------|--------|
| BulkMerge 	  | MergeMatchedAndFormula 		 | [Fiddle](https://dotnetfiddle.net/sgEyDR) |
| BulkUpdate 	  | UpdateMatchedAndFormula  	 | [Fiddle](https://dotnetfiddle.net/cTGNTh) |
| BulkSynchronize | SynchronizeMatchedAndFormula | [Fiddle](https://dotnetfiddle.net/zsroHG) |

## Related Solutions

- [Matched and Condition](doc-v2/matched-and-condition.md)
- [Matched and one NOT Condition](doc-v2/matched-and-one-not-condition.md)