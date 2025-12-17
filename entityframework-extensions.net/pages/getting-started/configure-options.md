---
Title: How to Configure Options in Entity Framework Extensions
MetaDescription: Learn how to configure Entity Framework Extensions options using lambdas, instances, and global settings to write cleaner, flexible, high-performance code.
LastMod: 2025-12-17
---

# EF Extensions - Configuring Options

People love using Entity Framework Extensions not only for performance, but also for the hundreds of options available.

In this article, you’ll learn the different ways you can configure options in our library so you can choose what fits your style best.

All configuration approaches shown in this guide apply to all bulk methods, such as
[BulkInsert](/bulk-insert), [BulkUpdate](/bulk-update), [BulkMerge](/bulk-merge), and others.

You will learn how to:

* [Choose between single-line expressions and code blocks](#single-line-expression-vs-code-block)
* [Use property selectors vs anonymous object selectors](#single-property-selector-vs-anonymous-object-selector)
* [Configure options inline with lambda expressions](#configuring-options-using-a-lambda-expression)
* [Reuse options by using an options instance](#configuring-options-using-an-instance)
* [Define global options for your entire application](#configuring-a-global-option)
* [Avoid common mistakes such as overriding options](#troubleshooting)

Whether you prefer short expressions or more explicit code, you’ll find an approach that feels natural to you.

## Single-line Expression vs Code Block

When configuring your options, you usually have two syntax choices:

* **Single-line expression:** Perfect when you only have one option. It’s shorter, cleaner, and easy to read.
* **Code block:** Ideal when you need to set multiple options — or when you prefer a consistent code structure.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

// Single-line expression
context.BulkInsert(list, options => options.BatchSize = 1000);

// Code block
context.BulkInsert(list, options => { 
   options.BatchSize = 1000;
   options.BatchTimeout = 60;
});
```

There’s no difference in how the library processes these options. It’s purely about readability and personal preference.

Both syntaxes can be used interchangeably across your project.

If you’re writing one-liners, the concise syntax works great. If you expect to add more settings later or prefer structured code, the block version is usually the better choice.

## Single Property Selector vs Anonymous Object Selector

Many options related to properties require selectors. Once again, you have two ways to write them:

* **Single property selector:** Cleaner when you only have one property.
* **Anonymous object selector:** Better when you have multiple properties — or when you want to stay consistent with more complex mappings.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

// Single property selector 
context.BulkInsert(list, options => options.ColumnPrimaryKeyExpression = x => x.ID);

// Anonymous object selector
context.BulkInsert(list, options => options.ColumnPrimaryKeyExpression = x => new { x.ID, x.ExternalID });
```

Both approaches are completely valid and produce the same result. It’s simply a matter of what fits best in your context.

This selector pattern is used by many options that work with entity properties, not just primary keys.

Almost all options that accept an expression also have an equivalent `List<string>` version. This is especially useful when your properties are not known at compile time:

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

UpdateWithSpecificKey(context, customers, new List<string> { nameof(Customer.ExternalID) });

public void UpdateWithSpecificKey(
    DbContext context,
    List<Customer> customers,
    List<string> customKeys)
{
    context.BulkUpdate(customers, options => options.ColumnPrimaryKeyNames = customKeys);
}
```

## Configuring Options Using a Lambda Expression

For most developers, configuring options directly inside the method call is the most convenient approach.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

context.BulkInsert(list, options => {
   options.BatchSize = 1000;
   options.BatchTimeout = 60;
   options.ColumnPrimaryKeyExpression = x => x.ID;
});
```

This approach keeps all settings close to the operation itself, making your code easier to read and maintain — especially when debugging or reviewing specific calls.

## Configuring Options Using an Instance

If you’re working with similar configurations across different parts of your application, using an options instance can help avoid duplication:

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

var customerOptions = context.Customers.CreateBulkOptions();
// or
var customerOptions = context.CreateBulkOptions<Customer>();

customerOptions.BatchSize = 1000;
customerOptions.BatchTimeout = 60;
customerOptions.ColumnPrimaryKeyExpression = x => x.ID;

context.BulkInsert(list, customerOptions);
```

This approach is especially useful when you’re dealing with shared logic, complex business rules, or when testing multiple configurations.


## Configuring a Global Option

You can also define default global settings that apply automatically to all future bulk operations by using `EntityFrameworkManager.BulkOperationBuilder`:

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

EntityFrameworkManager.BulkOperationBuilder = builder => {
   builder.BatchSize = 1000;
   builder.BatchTimeout = 60;
};
```

This approach is ideal when you want consistent behavior across your entire application without having to configure options for every call.


## Troubleshooting

### Overriding Your Options

One common mistake we often see — especially when configuring global options — is unintentionally overriding a previous configuration.

This behavior is expected, as assigning `EntityFrameworkManager.BulkOperationBuilder` multiple times replaces the previous builder entirely.

In the example below, we first assign a builder with `BatchSize = 1000`. Right after that, we assign a new builder that sets `BatchTimeout = 60`. The problem is that the first configuration is completely lost, because the second assignment replaces it entirely.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

EntityFrameworkManager.BulkOperationBuilder = builder => {
   builder.BatchSize = 1000;
};

EntityFrameworkManager.BulkOperationBuilder = builder => {
   builder.BatchTimeout = 60;
};
```

To fix this, make sure you configure everything within the same builder. Global options are typically configured once during application startup.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

EntityFrameworkManager.BulkOperationBuilder = builder => {
   builder.BatchSize = 1000;
   builder.BatchTimeout = 60;
};
```

This way, all your global settings are applied at once without accidentally discarding any previous values.

## Summary & Next Steps

You’ve now seen all the main ways to configure options in Entity Framework Extensions — from simple inline lambdas to reusable instances and global defaults.

There’s no “best” approach. Each one exists for a reason, and you can freely mix them depending on your needs. The goal is always the same: keep your code readable, avoid duplication, and stay in control of how your bulk operations behave.

Now that you know **how** to configure options, the next step is to learn **what you can configure**.

👉 Continue with **[Configuring Column Options](/configure-column-options)** to see how to control column mapping, keys, ignored properties, and other column-level behaviors.

This is where most real-world fine-tuning happens.