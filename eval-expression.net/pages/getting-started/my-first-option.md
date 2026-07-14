---
Title: My First Option with C# Eval Expression
MetaDescription: Learn how to configure options in C# Eval Expression, including Safe Mode, custom keywords, and dynamic methods, to customize how expressions are parsed and executed.
LastMod: 2026-07-14
---

# My First Option with C# Eval Expression

In this article, we will cover only a few options, but you can find the complete list on the [Options](/options) page. Understanding how [register methods](https://eval-expression.net/register-unregister) work will also help you better understand how options work.

In the following examples, we will use the default context, a local context, and a new static context to add options. All three approaches are valid. Which one is recommended depends on what you want to achieve.

* [Use option for safe mode](#use-option-for-safe-mode)
* [Use option to add keyword](#use-option-to-add-keyword)
* [Use option to add method](#use-option-to-add-method)


## Use Option for Safe Mode

The [Safe Mode](/options#safemode) option ensures that only registered members and types can be used in an expression.

When using safe mode, we recommend:

* Using a local or static context
* Unregistering all types first
* Limiting the maximum number of loops with the [MaxLoopIteration](/options#maxloopiteration) option
* Setting the `SafeMode` option to `true`
* Registering only the members and types you want users to be able to use

For example, the following code shows how easy it is to configure safe mode in the C# Eval Expression library:

```csharp
// @nuget: Z.Expressions.Eval
using Z.Expressions;

var evalContext = new EvalContext();
evalContext.UnregisterAll();
evalContext.MaxLoopIteration = 5;
evalContext.SafeMode = true;

evalContext.RegisterMember(typeof(System.Math));
var r1 = evalContext.Execute("Math.Min(1, 2)");
Console.WriteLine("1 - Result: " + r1);

try
{
    // `List` is no longer registered, so it cannot be used
    var r2 = evalContext.Execute("new List<int>()");
}
catch (Exception ex)
{
    Console.WriteLine("Cannot use `List` exception: " + ex.Message);
}
```

{% include component-try-it.html href='https://dotnetfiddle.net/Vy36z5' %}

## Use Option to Add Keyword

In the C# Eval Expression library, you can add your own keywords. While you cannot override existing keywords as easily, you can create new ones such as `isin`, which checks whether the current item is in a list.

In this example, we create an extension method named `IsIn` that checks whether an item is part of a list. Then, we register the `isin` keyword and bind it to the extension method so it can be used directly in an expression:

```csharp
// @nuget: Z.Expressions.Eval

using System;
using System.Collections.Generic;
using Z.Expressions;

public class Program
{
	public static void Main()
	{
		EvalManager.DefaultContext.RegisterExtensionMethod(typeof(CustomExtensions));
		EvalManager.DefaultContext.RegisterKeyword("isin", "IsIn");
		
		var list = new List<int>() { 1, 2, 3 };
		
		Console.WriteLine(Eval.Execute("2 isin list", new { list }));
		Console.WriteLine(Eval.Execute("4 isin list", new { list }));
	}
}

public static class CustomExtensions
{
	public static bool IsIn<T>(this T x, List<T> list)
	{
		return list.Contains(x);
	}
}
```

{% include component-try-it.html href='https://dotnetfiddle.net/EcwhUk' %}

## Use Option to Add Method

In the C# Eval Expression library, you can add your own methods.

If you can define a method at compile time, we strongly recommend [registering the method](/register-unregister) instead of creating it dynamically. Registered methods are faster, more secure, and easier to maintain.

In this first example, we create a simple custom method named `MinPlusOne` that returns the minimum value between two numbers and adds one:

```csharp
// @nuget: Z.Expressions.Eval
using Z.Expressions;

var evalContext = new EvalContext();

evalContext.AddMethod(@"
public int MinPlusOne(int A, int B)
{
	return (A < B ? A : B) + 1;
}
");

var result = evalContext.Execute<int>("MinPlusOne(Value1, Value2)", new { Value1 = 1, Value2 = 99 });
Console.WriteLine(result);
```

{% include component-try-it.html href='https://dotnetfiddle.net/4VXupA' %}

In this second example, we go one step further by creating a method dynamically. The method logic (formula) is provided by the user, allowing a user-defined expression to be injected directly into the method.

```csharp
// @nuget: Z.Expressions.Eval
using Z.Expressions;

string userInputFormula = "A == B || A == -1 || B == -1";

var evalContext = new EvalContext();
evalContext.UseCache = false;

evalContext.AddMethod($$"""public bool IsEqualsCustom(int A, int B) { return {{userInputFormula}}; }""");

Console.WriteLine(evalContext.Execute<bool>("IsEqualsCustom(1, 1)"));
Console.WriteLine(evalContext.Execute<bool>("IsEqualsCustom(1, 2)"));
Console.WriteLine(evalContext.Execute<bool>("IsEqualsCustom(1, -1)"));
```

{% include component-try-it.html href='https://dotnetfiddle.net/ZlZjx4' %}

## Summary

Adding options in the C# Eval Expression library is easy, but mastering all the [available options](/options) takes time. In most cases, you only need to [register types and methods](/register-unregister), which is the recommended approach.

However, for more advanced scenarios, options such as `SafeMode`, custom keywords, and dynamic methods allow you to customize exactly how expressions are parsed and executed.