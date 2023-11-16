---
Title: Bulk Options for Bulk Extensions | Customize Your Saving Experience
MetaDescription: Learn how to customize your EF6 and EF Core Bulk Extensions by customizing methods with bulk options. Over 100 options are available to cover any scenario - try it now.
LastMod: 2023-11-16
---

# Bulk Options

Entity Framework Extensions have been created to be fast but also to be extensible, with hundreds of options available. Understanding available options is essential to make your [Bulk Extensions](https://entityframework-extensions.net/bulk-extensions) more flexible, but also to know how to pass them:

- [Fluent API Options](#fluent-api-options)
- [BulkOperationOptions Variable](#bulkoperationoptions-variable)

## Fluent API Options

Passing options through a fluent API is recommended in Entity Framework Extensions. This technique is simple and matches the way you write your LINQ syntax.

You can pass 1 option by directly setting it or use braces to pass multiple options, such as

```csharp
// only work with 1 option
context.BulkInsert(invoices, options => options.IncludeGraph = true);

// work with multiple options
context.BulkInsert(invoices, options => { options.IncludeGraph = true });
context.BulkInsert(invoices, options => { 
    options.IncludeGraph = true;
    options.BatchTimeout = 180;
});
```

[Try it](https://dotnetfiddle.net/B5myBe)

## BulkOperationOptions Variable

Starting from [v8.101.0](/v8-101-0-0-efcore-8), you can now pass options by creating a new `Z.BulkOperations.BulkOperationOptions` instance.

You can either choose to:

- From a [DbSet](https://www.learnentityframeworkcore.com/dbset)
- Creating an instance with a generic type
- Creating an instance without a generic type

```csharp
// CREATE bulkOptions instance
var bulkOptions = context.Invoices.CreateBulkOptions();
var bulkOptions_Generic = new Z.BulkOperations.BulkOperationOptions<Invoice>();
var bulkOptions_NonGeneric = new Z.BulkOperations.BulkOperationOptions();

// SET Options
bulkOptions.IncludeGraph = true;
bulkOptions.BatchTimeout = 180;

// CALL a bulk method
context.BulkInsert(invoices, bulkOptions);
```

[Try it](https://dotnetfiddle.net/njEg4j)

## Conclusion

Whether you pass options with a fluent API or through a `BulkOperationOptions` variable is a personal choice. Both techniques are now supported, and sometimes, a technique might be more appropriate than another one, but by default, we recommend sticking with only one in your code.