---
Title: FREE Single Insert in EF Core and EF6
MetaDescription: Insert single entities in EF Core and EF6 for free with SingleInsert. Use the same options as BulkInsert, including InsertIfNotExists, custom keys, and InsertKeepIdentity, then upgrade to BulkInsert later without changing your logic.
LastMod: 2026-06-02
---

# EF Core Single Insert

`SingleInsert` is a **FREE** extension method provided by Entity Framework Extensions for EF Core and EF6.

It lets you insert a single entity while taking advantage of the same powerful options available in [Bulk Insert](/bulk-insert), such as `InsertIfNotExists`, custom keys, auditing, and more.

Use this method when you only need to insert one entity but still want access to the advanced insert features offered by Entity Framework Extensions.

You can also use `SingleInsert` in a loop to insert multiple entities for free. While this won't provide the performance benefits of a bulk operation, it allows you to start using the same API and options before deciding whether a [bulk solution](/bulk-extensions) fits your needs.

Since `SingleInsert` supports the same options as `BulkInsert`, most advanced scenarios and customization options are documented in the [Bulk Insert](/bulk-insert) documentation.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

// Easy to use
await context.SingleInsertAsync(customer);

// Easy to customize
await context.SingleInsertAsync(customer, options => options.InsertIfNotExists = true);
```

[Online Example](https://dotnetfiddle.net/HtncjX)

## Single Insert Examples

### Insert Multiple Entities for Free

While `SingleInsert` is designed to insert a single entity, you can use it in a loop to insert multiple entities for free.

This can be a great way to start using Entity Framework Extensions and its options without switching to a bulk operation. If performance becomes important later, you can often replace the loop with a single [Bulk Insert](/bulk-insert) call.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

foreach (var customer in customers)
{
    context.SingleInsert(customer);
}
```

[Online Example](https://dotnetfiddle.net/h5AYnz)

### Insert If Not Exists

When inserting a single entity, you can use the `InsertIfNotExists` option to prevent duplicate records from being created.

Entity Framework Extensions checks whether the row already exists before performing the insert. By default, the check uses your entity's mapped primary key, but you can also specify a custom key with the [ColumnPrimaryKeyExpression](/primary-key#using-columnprimarykeyexpression) or [ColumnPrimaryKeyNames](/primary-key#using-columnprimarykeynames) options.

This option is commonly used when importing data from external systems where duplicates may already exist.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

// Using mapped primary key
context.SingleInsert(customer,
    options => options.InsertIfNotExists = true);

// Using a custom key
context.SingleInsert(customer, options =>
{
    options.InsertIfNotExists = true;
    options.ColumnPrimaryKeyExpression = x => x.Code;
});
```

[Online Example](https://dotnetfiddle.net/5jFPpg)

### Insert with Identity Value

If your entity already contains a value for an identity column, use the `InsertKeepIdentity` option to preserve that value during the insert operation.

This is useful when restoring data, migrating records from another database, or importing entities that already have assigned identity values.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

var customer = new Customer
{
    CustomerID = 100,
    Name = "John"
};

context.SingleInsert(customer,
    options => options.InsertKeepIdentity = true);
```

[Online Example](https://dotnetfiddle.net/4JDHM1)

### More Examples

Need a scenario not covered here?

`SingleInsert` shares the same options and customization features as [Bulk Insert](/bulk-insert). Most examples shown in the Bulk Insert documentation can be adapted to `SingleInsert` by simply replacing the method name.

Many developers start with `SingleInsert` and later switch to `BulkInsert` when performance becomes important, without changing their business logic or configuration.

See the [Bulk Insert Examples](/bulk-insert#bulk-insert-example) section for additional scenarios and advanced options.

**[Contact us to discuss your scenario](/contact-us)**

## Summary

In this article, you learned how to use `SingleInsert` to insert entities with Entity Framework Extensions.

You saw how to:

* Insert a single entity with `SingleInsert`
* Insert multiple entities for free by using `SingleInsert` in a loop
* Prevent duplicate records with `InsertIfNotExists`
* Preserve identity values with `InsertKeepIdentity`
* Reuse the same options and configuration available in `BulkInsert`

When performance becomes important, you can often switch from `SingleInsert` to [Bulk Insert](/bulk-insert) while keeping the same business logic and options.
