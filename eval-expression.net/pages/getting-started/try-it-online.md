---
Title: Try C# Eval Expression Online with .NET Fiddle
MetaDescription: Learn how to try C# Eval Expression online with .NET Fiddle, run examples in your browser, and experiment without creating a project.
LastMod: 2026-07-13
---

# Try C# Eval Expression Online

You can try C# Eval Expression directly in your browser with [.NET Fiddle](https://dotnetfiddle.net/).

While learning C# Eval Expression, there is no need to create a project or manually install the NuGet package. Simply add the following directive at the top of your code:

```csharp
// @nuget: Z.Expressions.Eval
```

Then add the following `using` directive:

```csharp
using Z.Expressions;
```

.NET Fiddle automatically downloads the `Z.Expressions.Eval` NuGet package before compiling your code.

Everything runs directly in your browser, so you can start experimenting in just a few seconds.

## Try a Basic Evaluation

The following example shows how to evaluate an expression using two variables:

```csharp
// @nuget: Z.Expressions.Eval

using System;
using Z.Expressions;

public class Program
{
    public static void Main()
    {
        var result = Eval.Execute<int>("X + Y", new { X = 1, Y = 2 });

        Console.WriteLine(result);
    }
}
```

[Online Example](https://dotnetfiddle.net/utRTAj)

You can click the **Online Example** link to open and run the example directly in .NET Fiddle.

Alternatively, you can copy the code into a new .NET Fiddle.

You can then modify the expression, parameters, or values to experiment with your own scenarios.

## Try Any Documentation Example

You can use the same approach with almost any example from this documentation:

- Open .NET Fiddle.
- Add the NuGet directive at the top of the code.
- Copy the example you want to try.
- Add any required `using` directives.
- Click **Run**.

```csharp
// @nuget: Z.Expressions.Eval

using Z.Expressions;
```

This is often the fastest way to:

- Test an expression.
- Explore a feature.
- Try different options.
- Reproduce an issue.
- Share an example with someone else.

## More Online Examples

If you want to explore more features, you can browse our collection of online examples.

Every example is ready to run and edit in .NET Fiddle, making it easy to learn new features, experiment with different options, or adapt the code to your own scenarios.

[Browse All Online Examples](/online-examples)

## Summary

In this article, you learned how to use .NET Fiddle to try C# Eval Expression without creating a project or installing the NuGet package manually.

Continue with the following articles to learn more about C# Eval Expression:

- [My First Evaluation](/my-first-evaluation)
- [My First Compilation](/my-first-compilation)
- [My First LINQ Dynamic](/my-first-linq-dynamic)
- [My First Option](/my-first-option)
