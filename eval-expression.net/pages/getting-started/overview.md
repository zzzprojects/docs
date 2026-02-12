---
Title: Getting Started with C# Eval Expression | C# Expression Evaluator
MetaDescription: Learn about what the C# Eval Expression library is and what is an expression evaluator. Learn how to evaluate string code dynamically and why you should do it.
LastMod: 2026-02-19
---

# Overview

## What is the C# Eval Expression library?

The **C# Eval Expression** library is the most powerful and flexible expression evaluators you can find. It supports almost all the C# language and gets improved every year (See our [Release Notes](https://github.com/zzzprojects/Eval-Expression.NET/releases)). The library also includes multiple built-in logic features to make it easier for you to work with and evaluate C# dynamic expressions.

You can use this library to evaluate from simple math expressions to very complex C# code.

Do not worry if that is still unclear. We will explain everything you need to know about what this library can do in this **Getting Started**. But in a very simple example, it allows you to do something like this:

```csharp
// @nuget: Z.Expressions.Eval
using Z.Expressions;

var x = "1+2".Execute<int>(); // returns 3
```

That is sure a straightforward example, but imagine the string is not just `1+2`, but any C# code containing keywords such as `if`, `foreach`, or even LINQ methods. Would that not be awesome, right? Well, that is **exactly** what this library allows you to do.

### Installing the C# Eval Expression library

You can download the **C# Eval Expression** library from **NuGet**: [https://www.nuget.org/packages/Z.Expressions.Eval/](https://www.nuget.org/packages/Z.Expressions.Eval/)

You can also visit our [download](download) page to find all available links.

```csharp
PM> Install-Package Z.Expressions.Eval
```

```csharp
dotnet add package Z.Expressions.Eval
```


## What is an expression evaluator?

An expression evaluator is a library that allows you to execute an expression and get a result. This is especially useful for math expressions, but you can also take advantage of an expression evaluator in many other scenarios, such as:

* Creating HTML forms at runtime
* Applying dynamic validation rules
* Dynamically filtering a report (users enter their own filtering or ordering logic)
* Setting different environments per user
* Or even using it in a Discord bot to return the result of an expression

The possibilities of how you can use an expression evaluator are **almost infinite**. The more you understand the library, the more new use cases you will discover.

For example, you can evaluate a simple math expression like this:

```csharp
// @nuget: Z.Expressions.Eval
using Z.Expressions;

int result = Eval.Execute<int>("X + Y", new { X = 1, Y = 2 });
```

But you can also execute much more complex code, such as creating a list dynamically and filtering it using LINQ methods:

```csharp
// @nuget: Z.Expressions.Eval
using Z.Expressions;

int result = Eval.Execute<int>(@"
    var list = new List<int>() { 1, 2, 3, 4, 5 };
    var filter = list.Where(x => x < 4);
    return filter.Sum(x => x);
");
```
## What does the C# Eval Expression support?

The **C# Eval Expression** library is compatible with the following platforms:

* .NET 5+
* .NET Core 2+
* .NET Framework 4.0+

It supports nearly everything, from basic keywords to more advanced C# language features, including:

* Anonymous types
* Extension methods
* Lambda expressions
* LINQ methods
* Method overloads

The library also includes many useful features, such as:

* [SafeMode](https://eval-expression.net/options#safemode): Evaluate expressions in a secure environment
* [RegisterKeyword](https://eval-expression.net/register-unregister#registerkeyword): Register your own keyword to apply custom logic
* [AddMethod](https://eval-expression.net/my-first-option#use-option-to-add-method): Create methods dynamically to reuse them in your expressions

The library also includes multiple built-in logic behaviors. For example, it automatically calls the `ToList` method when the expected return type is a list, but the expression returns an `IEnumerable`.

It also handles `object` values with built-in logic that allows you to go deeper in your expressions and go beyond some C# language limitations.

Finally, some additional keywords have been added, such as `break all`. This keyword breaks **all** loops instead of only the current one.

## Is C# Eval Expression free?

Yes and no.

C# Eval Expression is free when:

* The expression is **up to 50 characters**
* You use any [LINQ Dynamic](/linq-dynamic) methods

For commercial usage, we recommend always [purchasing](/pricing) a license.

The library is also included in commercial products such as [Entity Framework Extensions](https://entityframework-extensions.net/) and [Dapper Plus](https://dapper-plus.net/).

## Summary

In this **Getting Started** section, you learned the basics of what an expression evaluator is and how to use it.

For developers who want to learn more about the **C# Eval Expression** library—or for those who already understand the basics—we highly recommend reading and completing all of the following **Getting Started** sections:

* [My First Evaluation](/my-first-evaluation)
* [My First Compilation](/my-first-compilation)
* [My First LINQ Dynamic](/my-first-linq-dynamic)
* [My First Option](/my-first-option)
* [Licensing](/licensing)
* [What to Read Next?](/what-to-read-next)
