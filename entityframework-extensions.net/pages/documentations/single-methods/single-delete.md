---
Title: FREE Single Delete in EF Core and EF6
MetaDescription: Delete single entities in EF Core and EF6 for free with SingleDelete. Use the same options as BulkDelete, including custom keys, conditional deletes, and more, then upgrade to BulkDelete later without changing your logic.
LastMod: 2026-06-02
---

# EF Core Single Delete

`SingleDelete` is a **FREE** extension method provided by Entity Framework Extensions for EF Core and EF6.

It lets you delete a single entity while taking advantage of the same powerful options available in [Bulk Delete](/bulk-delete), such as custom keys, conditional deletes, and more.

Use this method when you only need to delete one entity but still want access to the advanced delete features offered by Entity Framework Extensions.

You can also use `SingleDelete` in a loop to delete multiple entities for free. While this won't provide the performance benefits of a bulk operation, it allows you to start using the same API and options before deciding whether a [bulk solution](/bulk-extensions) fits your needs.

Since `SingleDelete` supports the same options as `BulkDelete`, most advanced scenarios and customization options are documented in the [Bulk Delete](/bulk-delete) documentation.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

// Easy to use
await context.SingleDeleteAsync(customer);

// Easy to customize
await context.SingleDeleteAsync(customer,
    options => options.ColumnPrimaryKeyExpression = x => x.Code);
```

[Online Example](https://dotnetfiddle.net/5T8RyG)

## Single Delete Examples

### Delete Multiple Entities for Free

While `SingleDelete` is designed to delete a single entity, you can use it in a loop to delete multiple entities for free.

This can be a great way to start using Entity Framework Extensions and its options without switching to a bulk operation. If performance becomes important later, you can often replace the loop with a single [Bulk Delete](/bulk-delete) call.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

foreach (var customer in customers)
{
    context.SingleDelete(customer);
}
```

[Online Example](https://dotnetfiddle.net/g0mj3D)

### Delete with a Custom Key

When deleting a single entity, you can use a custom key instead of your entity's mapped primary key to determine which row should be deleted.

This is useful when working with data from external systems where the primary key value is unavailable.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

// Using a custom key
context.SingleDelete(customer,
    options => options.ColumnPrimaryKeyExpression = x => x.Code);
```

[Online Example](https://dotnetfiddle.net/m1bYaK)

### Delete Only When Additional Conditions Match

When deleting a single entity, you can add extra conditions to ensure the row is only deleted if specific values still match those stored in the database.

This provides an additional layer of safety when multiple users or systems may be modifying the same data.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

context.SingleDelete(customer,
    options => options.DeleteMatchedAndConditionExpression = x => x.Status);
```

[Online Example](https://dotnetfiddle.net/bJ0EdG)

### More Examples

Need a scenario not covered here?

`SingleDelete` shares the same options and customization features as [Bulk Delete](/bulk-delete). Most examples shown in the Bulk Delete documentation can be adapted to `SingleDelete` by simply replacing the method name.

Many developers start with `SingleDelete` and later switch to `BulkDelete` when performance becomes important, without changing their business logic or configuration.

See the [Bulk Delete Examples](/bulk-delete#bulk-delete-example) section for additional scenarios and advanced options.

**[Contact us to discuss your scenario](/contact-us)**

## Summary

In this article, you learned how to use `SingleDelete` to delete entities with Entity Framework Extensions.

You saw how to:

* Delete a single entity with `SingleDelete`
* Delete multiple entities for free by using `SingleDelete` in a loop
* Delete entities by using a custom key
* Delete entities only when additional conditions match
* Reuse the same options and configuration available in `BulkDelete`

When performance becomes important, you can often switch from `SingleDelete` to [Bulk Delete](/bulk-delete) while keeping the same business logic and options.
