---
Title: Get started with your first option with C# Eval Expression
MetaDescription: Learn how to use options in C# Eval Expression to configure your eval context to align with your scenarios and requirements.
LastMod: 2024-03-22
---

# My First Option

In this article, we will only see a few options, but you can find all the [options here](/options). Understanding how [register methods](https://eval-expression.net/register-unregister) work will also help you understand better.

We will mix how options are added by either the default context, a local context, or a new static context. All three techniques are valid, and which one is recommended depends on what you want to achieve.

- [Use option for safe mode](#use-option-for-safe-mode)
- [Use option to add keyword](#use-option-to-add-keyword)
- [Use option to add method](#use-option-to-add-method)

## Use Option for Safe Mode

The [Safe Mode](/options#safemode) ensures that only members and types registered can be used for the expression.

To use safe mode, we recommend:
- A local or static context
- Unregistering all types first
- Limiting the maximum number of loops with the [MaxLoopIteration](/options#maxloopiteration) option
- Setting the `SafeMode` option to true
- Registering only what you want the user to be able to use

For example, we will demonstrate how easy it is to configure options in the C# Eval Expression library:

```csharp
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
catch(Exception ex)
{
	Console.WriteLine("Cannot use `List` exception: " + ex.Message);
}
```

{% include component-try-it.html href='https://dotnetfiddle.net/Vy36z5' %}

## Use Option to Add Keyword

In the C# Eval Expression library, you can add your own `keyword`. You cannot override existing keywords, but you can create new ones, such as `isin`, that will check if the current item is in the list. 

In this example, we will create an extension method named `IsIn` that checks if an item is part of a list. Then, we will create the keyword `isin` that will be bound to this extension method and can be used directly in the expression:

```csharp
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

Here are some suggestions for improving the grammar and simplifying the vocabulary in your article:

## Use Option to Add Method

In the C# Eval Expression library, it's possible to add your own method. If you can create the method in a non-dynamic way, you should always prefer this method and [register your method](/register-unregister) over creating a method dynamically.

In this example, we will create a method dynamically that is supposed to compare three numbers and use the formula from user input to determine if they are equal or not.

```csharp
string userInputFormula = "A == B || A == -1 || B == -1";

var evalContext = new EvalContext();
evalContext.UseCache = false;		

evalContext.AddMethod($$"""public bool IsEqualsCustom(int A, int B) { return {{userInputFormula}}; }""");

Console.WriteLine(evalContext.Execute<bool>("IsEqualsCustom(1, 1)")); 
Console.WriteLine(evalContext.Execute<bool>("IsEqualsCustom(1, 2)")); 
Console.WriteLine(evalContext.Execute<bool>("IsEqualsCustom(1, -1)")); 
```

{% include component-try-it.html href='https://dotnetfiddle.net/ZlZjx4' %}

## Conclusion

Adding options in the C# Eval Library is easy, but mastering all the [available options](/options) will surely take more time. In most cases, you only need to [register types](/register-unregister), but people with advanced scenarios might need to better understand those advanced options.
