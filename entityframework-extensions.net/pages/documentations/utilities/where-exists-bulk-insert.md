---
Title: WhereExistsBulkInsert: Insert Only If a Match Exists
MetaDescription: Learn how to use WhereExistsBulkInsert to insert entities only when a matching row exists. Customize the existence check with custom keys and additional conditions.
LastMod: 2026-06-01
---

# WhereExistsBulkInsert with Entity Framework Extensions

The `WhereExistsBulkInsert` method performs a bulk insert only when a matching row is found in a query you specify.

It is the opposite of [WhereNotExistsBulkInsert](/where-not-exists-bulk-insert), which inserts entities only when no matching row is found.

Like `WhereNotExistsBulkInsert`, the `Where` filter determines which rows participate in the existence check. It does not filter the entities being inserted.

For example, suppose you want to copy customers from one tenant to another. You only want to insert customers into the destination tenant if those customers already exist in the source tenant.

For this example, assume the source tenant (`sourceTenantID = 1`) already contains `Customer_A` and `Customer_B`, while `Customer_C` only exists in another tenant.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

var sourceTenantID = 1;
var destinationTenantID = 3;

var customers = new List<Customer>();

customers.Add(new Customer() { CustomerID = 1, Code = "A", Name = "Customer_A", TenantID = destinationTenantID });
customers.Add(new Customer() { CustomerID = 2, Code = "B", Name = "Customer_B", TenantID = destinationTenantID });
customers.Add(new Customer() { CustomerID = 3, Code = "C", Name = "Customer_C", TenantID = destinationTenantID });

context.Customers.Where(x => x.TenantID == sourceTenantID).WhereExistsBulkInsert(customers, options => {
    // Match customers by `CustomerID` only.
    // This ignores `TenantID`, allowing customers from `sourceTenantID`
    // to be matched with customers in another tenant.
    options.ColumnPrimaryKeyExpression = x => x.CustomerID;
});
```

[Online Example](https://dotnetfiddle.net/0l89JH)

In this example, the library checks whether each customer exists in the source tenant before inserting it into the destination tenant.

* Customer_A and Customer_B are inserted because matching customers exist in `sourceTenantID = 1`.
* Customer_C is skipped because no matching customer exists in `sourceTenantID = 1`.

This method is useful when records should only be copied, synchronized, or inserted if they already exist in a specific subset of data.

## WhereExistsBulkInsert Example

### Match with a Custom Key

You can customize how rows are matched during the existence check by using the `QueryFilterPrimaryKeyExpression` or `QueryFilterPrimaryKeyNames` options.

See [WhereNotExistsBulkInsert - Match with a Custom Key](/where-not-exists-bulk-insert#match-with-a-custom-key) for a complete example.

### Match with a Custom Key and Formula

You can add additional conditions to the existence check by using the `QueryFilterPrimaryKeyAndFormula` option.

See [WhereNotExistsBulkInsert - Match with a Custom Key and Formula](/where-not-exists-bulk-insert#match-with-a-custom-key-and-formula) for a complete example.

### More Examples

In addition to the options specific to `WhereExistsBulkInsert`, all options available for `BulkInsert` can also be used, including:

* [Insert with Identity Value](/bulk-insert#insert-with-identity-value)
* [Insert without Outputting Values](/bulk-insert#insert-without-outputting-values)
* [Insert with Rows Affected](/bulk-insert#insert-with-rows-affected)

## Summary

The `WhereExistsBulkInsert` method is the inverse of `WhereNotExistsBulkInsert`. Instead of inserting entities when no matching row is found, entities are inserted only when a matching row exists in the query you specify.

This is particularly useful when copying or synchronizing data from a source dataset and only records that already exist in that dataset should be inserted.

You can customize the existence check by using custom keys and additional matching conditions, just as you would with `WhereNotExistsBulkInsert`.