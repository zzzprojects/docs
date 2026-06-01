---
Title: WhereNotExistsBulkInsert: Insert Only If No Match Exists in a Query
MetaDescription: Learn how to use WhereNotExistsBulkInsert to insert entities only when no matching row exists in a filtered query. Supports custom keys and additional matching conditions.
LastMod: 2026-06-01
---

# WhereNotExistsBulkInsert with Entity Framework Extensions

The `WhereNotExistsBulkInsert` method performs a bulk insert only when no matching row is found in a query you specify.

Unlike [BulkInsert + InsertIfNotExists](/bulk-insert#insert-if-not-exists), which checks the entire destination table, `WhereNotExistsBulkInsert` lets you define which rows should be considered during the existence check by using a LINQ query.

The `Where` filter is used only to determine which rows participate in the existence check. It does not filter the entities being inserted.

This is useful when your business rules require existence checks to be limited to a specific subset of data rather than the entire table.

For example, in a multi-tenant application, the same customer may exist in multiple tenants. You may want to insert a customer only when no matching customer already exists for the current tenant.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

var tenantID = 1;

context.Customers
    .Where(x => x.TenantID == tenantID)
    .WhereNotExistsBulkInsert(customers);
```

[Online Example](https://dotnetfiddle.net/jXHSSB)

In this example, the library only checks customers that belong to the specified tenant when determining whether a customer already exists. If a matching customer is found within that tenant, the insert is skipped. Otherwise, the customer is inserted, even if a matching customer exists in another tenant.


## WhereNotExistsBulkInsert Example

### Match with a Custom Key

By default, Entity Framework Extensions determines whether a row already exists by using the entity's mapped primary key.

In some scenarios, your data comes from an external system and the primary key values are different from those stored in your database. For example, the same customer may have a different `CustomerID` in each tenant, but share the same business identifier such as `Code`.

In the example below, the existence check is performed against customers in the current tenant and matches rows using the `Code` property instead of the entity's primary key.

You can specify a custom key by using the `QueryFilterPrimaryKeyExpression` or `QueryFilterPrimaryKeyNames` options.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

var tenantID = 1;

// Using `QueryFilterPrimaryKeyExpression`
context.Customers
    .Where(x => x.TenantID == tenantID)
    .WhereNotExistsBulkInsert(customers, options => {
        options.QueryFilterPrimaryKeyExpression = x => x.Code;
    });

// Using `QueryFilterPrimaryKeyNames`
var customKeys = new List<string>() { nameof(Customer.Code) };
context.Customers
    .Where(x => x.TenantID == tenantID)
    .WhereNotExistsBulkInsert(customers, options => {
        options.QueryFilterPrimaryKeyNames = customKeys;
    });
```

[Online Example](https://dotnetfiddle.net/YWWJs6)

### Match with a Custom Key and Formula

Sometimes a custom key is not enough and additional conditions must be satisfied before a row is considered a match.

For example, your application may use soft deletes. In this case, you may want to match customers by `Code`, but only against customers that are not soft-deleted.

In the example below, the existence check is performed against customers in the current tenant and matches rows using the `Code` property. The `QueryFilterPrimaryKeyAndFormula` option adds an additional condition so that only customers with `IsSoftDeleted = 0` are considered during the existence check.

This allows a customer to be inserted even if a matching soft-deleted customer already exists.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

var tenantID = 1;

context.Customers
    .Where(x => x.TenantID == tenantID)
    .WhereNotExistsBulkInsert(customers, options => {
        options.QueryFilterPrimaryKeyExpression = x => x.Code;
        options.QueryFilterPrimaryKeyAndFormula = "IsSoftDeleted = 0";
});
```

[Online Example](https://dotnetfiddle.net/8mgxJw)

### More Examples

In addition to the options specific to `WhereNotExistsBulkInsert`, all options available for `BulkInsert` can also be used, including:
- [Insert with Identity Value](/bulk-insert#insert-with-identity-value)
- [Insert without Outputting Values](/bulk-insert#insert-without-outputting-values)
- [Insert with Rows Affected](/bulk-insert#insert-with-rows-affected)

Need a scenario that isn't covered here?

There's a good chance we already support it.

**[Contact us to discuss your scenario](/contact-us)**.

## Real-Life Scenarios

`WhereNotExistsBulkInsert` is useful when a record should be considered unique only within a specific subset of data rather than across the entire table.

Common scenarios include:

* **Multi-tenant applications:** A customer may exist in multiple tenants, but should only be checked against customers belonging to the current tenant.
* **Organizations or companies:** An employee number, customer code, or product code may be unique within a company but reused by another company.
* **Projects:** A task code or ticket number may need to be unique within a project while allowing the same value in other projects.
* **Events:** An attendee may already be registered for one event but should still be allowed to register for a different event.
* **Regional data:** A distributor code, store code, or customer reference may only need to be unique within a specific country, state, or region.
* **Soft-delete systems:** Records marked as deleted should be ignored during the existence check, allowing a new active record to be inserted.
* **Archived data:** Historical records may be excluded from the existence check so that only active records are considered.
* **Import workflows:** New records may need to be checked only against data from a specific import batch, version, or reporting period.

In all these scenarios, checking the entire table would produce incorrect results. `WhereNotExistsBulkInsert` lets you define exactly which rows should participate in the existence check.

## Summary

The `WhereNotExistsBulkInsert` method lets you insert entities only when no matching row exists within a filtered set of data. This is particularly useful for multi-tenant applications, soft-delete systems, and other scenarios where uniqueness should be enforced only within a specific scope.

You can further customize how existing rows are matched by using a custom key through `QueryFilterPrimaryKeyExpression` or `QueryFilterPrimaryKeyNames`, and add additional conditions with `QueryFilterPrimaryKeyAndFormula`.

Like other bulk operations, `WhereNotExistsBulkInsert` also supports the standard `BulkInsert` options, allowing you to customize performance, identity handling, and result tracking to fit your requirements.