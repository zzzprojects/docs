---
Name: Getting Started
---

# Overview

## What is the C# Eval Expression library?

The C# Eval Expression library is the most powerful expression evaluator. It supports nearly all the C# languages and keeps getting improved every year. The library also has multiple built-in logic to make it easier on your side to work and evaluate dynamic expressions.

### Installing

You can find the C# Eval Expression library on NuGet. See our <a href="/download">download</a> page.

The NuGet package support:
.NET 5+ (.NET Core 5+)
.NET Standard 2.0+
.NET Framework 4.0+

## What is an expression evaluator?

An expression evaluator is a tool that allows you to input an expression and get the result. That is especially handy for math expressions but can use for any kind of scenario such as:

- Filtering a report dynamically (user input)
- Setting different environments by user
- Or even customizing a discord bot!

For example, you can evaluate a straightforward math expression such as

```csharp
int result = Eval.Execute<int>("X + Y", new { X = 1, Y = 2});
```

But you can also execute way more complex code, such as creating a list dynamically and filtering it using LINQ methods:

```sharp
int result = Eval.Execute<int>(@"
    var list = new List<int>() { 1, 2, 3, 4, 5 };
    var filter = list.Where(x => x < 4);
    return filter.Sum(x => x);");
```

## What does the C# Eval Expression support?

The C# Eval Epxpression library supports nearly everything from basic keywords to more advanced usage of the C# language:

- Anonymous Type
- Extension Method
- Lambda Expression
- LINQ Methods
- Method Overloads

The library also has multiple built-in logic, for example calling the `ToList` method automatically when the return type is currently an `IEnumerable,` but a list is expected or some new keywords such as `break all` that will break all loops.

## Is C# Eval Expression free?

The C# Eval Expression is free for up to 50 characters or any LINQ Dynamic methods.

For commercial usage, we recommend always purchasing a license.

## Conclusion

In this overview, we have not seen much but don't worry. Some step-by-step tutorials will help you get started and let you know more about how the C# Eval Expression library could help you in your current project:

- [My First Evaluation](/my-first-evaluation)
- [My First Compilation](/my-first-compilation)
- [My First LINQ Dynamic](/my-first-linq-dynamic)
- [Licensing](/licensing)
- [What to Read Next?](/what-to-read-next)