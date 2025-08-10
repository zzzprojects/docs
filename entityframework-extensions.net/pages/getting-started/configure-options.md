---
Title: How to Configure Options in Entity Framework Extensions
MetaDescription: Learn the easiest ways to configure options in Entity Framework Extensions. From lambda expressions to global settings, this beginner-friendly guide helps you write cleaner and more flexible code.
LastMod: 2025-08-09
---

# How to Configure Options in Entity Framework Extensions /n A Beginner’s Guide

People love using Entity Framework Extensions not just for the performance—but also for the hundreds of options available.

In this article, you’ll learn the different ways to configure options in our library so you can find the one that fits your style. Whether you like writing short expressions or prefer to be more explicit, there’s a way that will feel natural to you.

---

## Single-line Expression vs Code Block

When configuring your options, you usually have two syntax choices:

* **Single-line expression:** Perfect when you only have one option. It’s shorter, cleaner, and easy to read.
* **Code block:** Ideal when you need to set multiple options—or when you always prefer a consistent code structure.

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

There’s no difference in how our library processes these. It’s all about readability and what you personally prefer. If you’re writing one-liners, go with the concise syntax. If you like being more structured or expect to add more settings later, choose the block version.

---

## Single Property Selector vs Anonymous Object Selector

Many options related to properties require selectors. And once again, you have two ways to write them:

* **Single property selector:** Cleaner when you only have one property.
* **Anonymous object selector:** Better for multiple properties—or if you want to stay consistent with more complex mappings.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

// Single property selector 
context.BulkInsert(list, options => options.ColumnPrimaryKeyExpression = x => x.ID);

// Anonymous object selector
context.BulkInsert(list, options => options.ColumnPrimaryKeyExpression = x => new { x.ID, x.ExternalID });
```

Both approaches are completely valid and produce the same result. It's just a matter of what fits best in your context.

Also, almost all options that accept an expression also have an equivalent `List<string>` version. This is useful when your properties are not known at compile time:

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

UpdateWithSpecificKey(context, customers, new List<string> { nameof(Customer.ExternalID) });
public void UpdateWithSpecificKey(DbContext context, List<Customer> customers, List<string> customKeys)
{
	context.BulkUpdate(list, options => options.ColumnPrimaryKeyNames = customKeys);
}
```

---

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

This way, you keep all the settings close to the operation itself, which makes your code easier to read and maintain—especially when debugging or reviewing specific calls.

---

## Configuring Options Using an Instance

If you’re working with similar configurations across different parts of your application, using an instance can help avoid duplication:

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

This method is especially useful when you're dealing with complex business logic or testing multiple configurations.

---

## Configuring a Global Option

You can also define default global settings that will apply automatically to all future bulk operations by using `EntityFrameworkManager.BulkOperationBuilder`:

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

EntityFrameworkManager.BulkOperationBuilder = builder => {
   builder.BatchSize = 1000;
   builder.BatchTimeout = 60;
};
```

This is perfect when you want consistent behavior across your whole application without having to set options every time.

---

## Troubleshooting

### Overriding Your Options

One common mistake we often see—especially when configuring global options—is unintentionally overriding the previous configuration.

In the example below, we first set a builder with `BatchSize = 1000`, but right after that, we assign a new builder that sets `BatchTimeout = 60`. The problem? The first configuration is completely lost because the second assignment replaces it entirely.

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

To fix this, make sure you configure everything within the same builder:

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

EntityFrameworkManager.BulkOperationBuilder = builder => {
   builder.BatchSize = 1000;
   builder.BatchTimeout = 60;
};
```

This way, all your global settings are applied at once without accidentally discarding any previous values.

---

## Conclusion

Whether you prefer keeping things short and simple or want full control over your configuration, Entity Framework Extensions gives you the flexibility to work the way you want.

Try each method and pick the one that fits your coding style. You can mix and match too—there’s no wrong way here. The goal is to make your development smoother while getting top-tier performance from your database operations 💪
