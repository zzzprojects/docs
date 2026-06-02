---
Title: FREE Single Merge in EF Core and EF6
MetaDescription: Add or update single entities in EF Core and EF6 for free with SingleMerge. Use the same options as BulkMerge, including custom keys, identity value preservation, and more, then upgrade to BulkMerge later without changing your logic.
LastMod: 2026-06-02
---

# EF Core Single Merge

`SingleMerge` is a **FREE** extension method provided by Entity Framework Extensions for EF Core and EF6.

It lets you insert or update a single entity while taking advantage of the same powerful options available in [Bulk Merge](/bulk-merge), such as custom keys, identity value preservation, and more.

When you perform a merge, it behaves as follows:

* Rows that match the entity key are **UPDATED**.
* Rows that do not match any existing record are **INSERTED**.

Use this method when you only need to merge one entity but still want access to the advanced upsert features offered by Entity Framework Extensions.

You can also use `SingleMerge` in a loop to merge multiple entities for free. While this won't provide the performance benefits of a bulk operation, it allows you to start using the same API and options before deciding whether a [bulk solution](/bulk-extensions) fits your needs.

Since `SingleMerge` supports the same options as `BulkMerge`, most advanced scenarios and customization options are documented in the [Bulk Merge](/bulk-merge) documentation.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

// Easy to use
await context.SingleMergeAsync(customer);

// Easy to customize
await context.SingleMergeAsync(customer,
    options => options.ColumnPrimaryKeyExpression = x => x.Code);
```

[Online Example](https://dotnetfiddle.net/B2idlU)

## Single Merge Examples

### Merge Multiple Entities for Free

While `SingleMerge` is designed to merge a single entity, you can use it in a loop to merge multiple entities for free.

This can be a great way to start using Entity Framework Extensions and its options without switching to a bulk operation. If performance becomes important later, you can often replace the loop with a single [Bulk Merge](/bulk-merge) call.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

foreach (var customer in customers)
{
    context.SingleMerge(customer);
}
```

[Online Example](https://dotnetfiddle.net/zSMmnt)

### Merge with a Custom Key

When merging a single entity, you can use a custom key instead of your entity's mapped primary key to determine whether the row should be inserted or updated.

This is useful when importing data from external systems where matching is based on a business identifier such as a code, SKU, or external ID.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

// Using a custom key
context.SingleMerge(customer,
    options => options.ColumnPrimaryKeyExpression = x => x.Code);
```

[Online Example](https://dotnetfiddle.net/aV6F4k)

### Merge with Identity Value

By default, when a new row is inserted during a merge, the database generates the value for identity columns.

Use the `MergeKeepIdentity` option when you want to preserve the identity value from your entity instead.

This is useful when importing existing data where identity values must be preserved.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

var customer = new Customer
{
    CustomerID = 100,
    Name = "John"
};

context.SingleMerge(customer,
    options => options.MergeKeepIdentity = true);
```

[Online Example](https://dotnetfiddle.net/Gn3Pij)

### More Examples

Need a scenario not covered here?

`SingleMerge` shares the same options and customization features as [Bulk Merge](/bulk-merge). Most examples shown in the Bulk Merge documentation can be adapted to `SingleMerge` by simply replacing the method name.

Many developers start with `SingleMerge` and later switch to `BulkMerge` when performance becomes important, without changing their business logic or configuration.

See the [Bulk Merge Examples](/bulk-merge#bulk-merge-example) section for additional scenarios and advanced options.

**[Contact us to discuss your scenario](/contact-us)**

## Summary

In this article, you learned how to use `SingleMerge` to insert or update entities with Entity Framework Extensions.

You saw how to:

* Merge a single entity with `SingleMerge`
* Merge multiple entities for free by using `SingleMerge` in a loop
* Match rows by using a custom key
* Preserve identity values with `MergeKeepIdentity`
* Reuse the same options and configuration available in `BulkMerge`

When performance becomes important, you can often switch from `SingleMerge` to [Bulk Merge](/bulk-merge) while keeping the same business logic and options.