---
Title: Execute LINQ Dynamic Expression with List and EF Core
MetaDescription: Learn how to use LINQ Dynamic query to execute methods such as Select, OrderBy, or Where clause dynamically.
---

# My First Option

In this article, we will see a few options, but our library offer more.

We will mixte how options is added by either the default context, a local context, or a new static context. All three techniques are valid and which one is recommended highly depends on what you want to achieve.

- [Use option to register type](#use-option-to-register-type)
- [Use option for safe mode](#use-option-for-safe-mode)
- [Use option to add keyword](#use-option-to-add-keyword)
- [Use option to add method](#use-option-to-add-method)

## Use option to register type

## Use option for safe mode

The safe mode option lets the user still use the library, but it's only allowed to use the registered type.

For using safe mode, we recommend:
- A local or static context
- Unregister first all types
- Limit the maximum number of loop with the `MaxLoop` option
- Set the `SafeMode` option to true

For example, the following code will work safe mode but will not work...
```csharp
```

In this example, we will create a new static context that we will use only when we know we are executing user input. We will create a list of customers and let the user input filter what would be returned.

## Use option to add keyword

## Use option to add method

## Conclusion

In this getting started tutorial, you learned how to use LINQ Dynamic with the `WhereDynamic` method, the `Execute` method, and the existence of all other supported dynamic methods.

If you already know how to use LINQ, using LINQ Dynamic should be very straightforward as the syntax is the same.