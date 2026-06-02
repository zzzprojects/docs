---
Title: FREE Single Update in EF Core and EF6
MetaDescription: Update single entities in EF Core and EF6 for free with SingleUpdate. Use the same options as BulkUpdate, including custom keys, updating specific properties, and more, then upgrade to BulkUpdate later without changing your logic.
LastMod: 2026-06-02
---

# EF Core Single Update

`SingleUpdate` is a **FREE** extension method provided by Entity Framework Extensions for EF Core and EF6.

It lets you update a single entity while taking advantage of the same powerful options available in [Bulk Update](/bulk-update), such as custom keys, updating specific properties, and more.

Use this method when you only need to update one entity but still want access to the advanced update features offered by Entity Framework Extensions.

You can also use `SingleUpdate` in a loop to update multiple entities for free. While this won't provide the performance benefits of a bulk operation, it allows you to start using the same API and options before deciding whether a [bulk solution](/bulk-extensions) fits your needs.

Since `SingleUpdate` supports the same options as `BulkUpdate`, most advanced scenarios and customization options are documented in the [Bulk Update](/bulk-update) documentation.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

// Easy to use
await context.SingleUpdateAsync(customer);

// Easy to customize
await context.SingleUpdateAsync(customer,
    options => options.ColumnInputExpression = x => new { x.Name });
```

[Online Example](https://dotnetfiddle.net/VMICYy)

## Single Update Examples

### Update Multiple Entities for Free

While `SingleUpdate` is designed to update a single entity, you can use it in a loop to update multiple entities for free.

This can be a great way to start using Entity Framework Extensions and its options without switching to a bulk operation. If performance becomes important later, you can often replace the loop with a single [Bulk Update](/bulk-update) call.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

foreach (var customer in customers)
{
    context.SingleUpdate(customer);
}
```

[Online Example](https://dotnetfiddle.net/ojYDRH)

### Update with a Custom Key

When updating a single entity, you can use a custom key instead of your entity's mapped primary key to locate the row that should be updated.

This is useful when synchronizing data from external systems where your primary key value is unavailable.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

// Using mapped custom key
context.SingleUpdate(customer,
    options => options.ColumnPrimaryKeyExpression = x => x.Code);
```

[Online Example](https://dotnetfiddle.net/ec8ihN)

### Update Only Specific Properties

By default, `SingleUpdate` updates all mapped properties.

You can use `ColumnInputExpression` to specify exactly which properties should be included in the update operation.

This is useful when you only want to update a subset of fields and leave all other values unchanged.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

context.SingleUpdate(customer,
    options => options.ColumnInputExpression = x => new
    {
        x.Name
    });
	
context.SingleUpdate(customer, 
	options => options.IgnoreOnUpdateExpression = x => x.Code);
```

[Online Example](https://dotnetfiddle.net/LhMKDy)

### More Examples

Need a scenario not covered here?

`SingleUpdate` shares the same options and customization features as [Bulk Update](/bulk-update). Most examples shown in the Bulk Update documentation can be adapted to `SingleUpdate` by simply replacing the method name.

Many developers start with `SingleUpdate` and later switch to `BulkUpdate` when performance becomes important, without changing their business logic or configuration.

See the [Bulk Update Examples](/bulk-update#bulk-update-example) section for additional scenarios and advanced options.

**[Contact us to discuss your scenario](/contact-us)**

## Summary

In this article, you learned how to use `SingleUpdate` to update entities with Entity Framework Extensions.

You saw how to:

* Update a single entity with `SingleUpdate`
* Update multiple entities for free by using `SingleUpdate` in a loop
* Match entities with a custom key
* Update only specific properties
* Reuse the same options and configuration available in `BulkUpdate`

When performance becomes important, you can often switch from `SingleUpdate` to [Bulk Update](/bulk-update) while keeping the same business logic and options.
