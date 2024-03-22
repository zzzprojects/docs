---
Title: Getting Started with C# Eval Expression | C# Expression Evaluator
MetaDescription: Learn about what the C# Eval Expression library is and what is an expression evaluator. Learn how to evaluate string code dynamically and why you should do it.
LastMod: 2024-03-22
---

# Overview

## What is the C# Eval Expression library?

The C# Eval Expression library is the most powerful and flexible expression evaluator you can find. It supports nearly all the C# languages and gets improved every year. The library also has multiple built-in logic to make it easier on your side to work and evaluate C# dynamic expressions.

Do not worry if that is still unclear because we will explain and cover more about what this library can do in this getting started section. But in a simple example, it allows you to do something like this:

```csharp
var x = "1+2".Execute<int>(); // return 3
```

That is sure a straightforward code, but imagine the string instead of being 1+2 to be any C# code such as `if`, `foreach`, or even LINQ methods. Would it not be awesome? Well, that is what this library exactly allows you to do.

### Installing

You can download the C# Eval Expression library on NuGet. See our <a href="/download">download</a> page to find the link.

C# Eval Expression is compatible with .NET, .NET Core and .NET Framework:

- .NET 5+
- .NET Core 2+
- .NET Framework 4.0+

## What is an expression evaluator?

An expression evaluator is a library that allows executing an expression and getting the result. That is especially useful for math expressions, but you can also use the advantage of an expression evaluator in various amount of scenarios, such as:

- Applying dynamically validation rules 
- Filtering dynamically a report (users enter their own filtering/ordering logic)
- Setting different environments by user
- Or even within a Discord bot to let it return a result of an expression

The possibility of how you could use an expression evaluator is infinite, and the more you understand the library, the more you can find additional ways to use it.

For example, you can evaluate a simple math expression such as

```csharp
int result = Eval.Execute<int>("X + Y", new { X = 1, Y = 2});
```

But you can also execute way more complex code, such as creating a list dynamically and filtering it using LINQ methods:

```csharp
int result = Eval.Execute<int>(@"
    var list = new List<int>() { 1, 2, 3, 4, 5 };
    var filter = list.Where(x => x < 4);
    return filter.Sum(x => x);");
```

## What does the C# Eval Expression support?

The C# Eval Expression library supports nearly everything from basic keywords to more advanced usage of the C# language, including:

- Anonymous Type
- Extension Method
- Lambda Expression
- LINQ Methods
- Method Overloads

The library also has multiple built-in logic. For example, the library automatically calls the `ToList` method when the return type is an `IEnumerable,` but the return expected is a list. 

Also, some new useful keywords, such as `break all` has been added. All loops will be `break` instead of only the current loop.

## Is C# Eval Expression free?

Yes and no. The C# Eval Expression is free up to 50 characters or when using any [LINQ Dynamic](/linq-dynamic) methods.

For commercial usage, we recommend always [purchasing](/pricing) a license.

## Conclusion

In this getting started, we just learned the base of what is an expression evaluator and how to use it.

For developers that want to know more about our C# Eval Expression library or even those that already understand how to use it, we highly recommend reading and completing all of our getting started sections:

- [My First Evaluation](/my-first-evaluation)
- [My First Compilation](/my-first-compilation)
- [My First LINQ Dynamic](/my-first-linq-dynamic)
- [Licensing](/licensing)
- [What to Read Next?](/what-to-read-next)