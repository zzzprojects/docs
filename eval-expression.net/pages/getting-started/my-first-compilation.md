---
Title: My First Compilation with C# Eval Expression | Getting Started
MetaDescription: Learn how to compile C# expressions at runtime for massive performance gains. Discover variables, return types, and dynamic execution tips.
LastMod: 2026-05-04
---

# My First Compilation with C# Eval Expression

In this tutorial, we will assume that you have read and understood the concept we introduced in the [My First Evaluation](/my-first-evaluation) getting started. If you have not read it yet, make sure you start with this previous tutorial, as we might skip the explanation of a few concepts.

So, what is the major difference between executing and compiling dynamic code?

- The `Compile` method compiles the expression into a method
- The `Execute` method calls the `Compile` method under the hood, and then executes the returned compiled method.

When executing the same expression multiple times with the `Execute` method, it is already fast, as we retrieve the compiled method from the cache. 

However, it still takes some time because we need to create a cache key, which has a performance cost. So when you need to execute the same expression multiple times, you should store the compiled method in a local or static variable.

How much faster? Using directly the compiled method returned from the `Compile` directly can be 1000x faster than using the `Execute` method. It surely does not make any difference if you are only looping over 10 items, as both ways, it will take nearly 0ms. However, if you need to evaluate millions of items, it makes a massive difference between a few milliseconds and a few seconds.

{% include component-try-it.html href='https://dotnetfiddle.net/W2qpBX' %}

In this tutorial, you will learn how to use our library to:

- [Compile a C# expression](#compile-a-c-expression)
- [Compile a C# expression with return type](#compile-a-c-expression-with-return-type)
- [Compile a C# expression with variable names](#compile-a-c-expression-with-variable-names)
- [Compile a C# expression from a string](#compile-a-c-expression-from-a-string)
- [Compile a C# expression from a context](#compile-a-c-expression-from-a-context)

## Compile a C# expression

Like the `Execute` method, the `Compile` method takes as the first parameter the expression to compile.

In this example, we dynamically create a list of `int` and filter it using LINQ to return only items greater than 2. The expression is compiled into a method and then executed. The `Func<List<int>>` return type will be explained in the next example.

```csharp
// @nuget: Z.Expressions.Eval
using Z.Expressions;

// NOTE: The returned list contains "3" and "4"
var compiled = Eval.Compile<Func<List<int>>>(@"
var list = new List<int>() { 1, 2, 3, 4 };
return list.Where(x => x > 2).ToList();
");

var list = compiled();
```

{% include component-try-it.html href='https://dotnetfiddle.net/VnYJP7' %}  

## Compile a C# expression with return type

In the last example, we successfully compiled an expression, but what exactly is the `Func<List<int>>` for?

The `Func<List<int>>` means that the `Compile` method should create a method that takes no parameters and returns a `List<int>`. Usually, the `Compile` method takes either an `Action` or a `Func`.

- An `Action` should be used when the method doesn't return any type (like when you create a `void` method).
- A `Func` should be used when the compiled method should returns a specific type

You can learn more about [Action](https://learn.microsoft.com/en-us/dotnet/api/system.action-1) and [Func](https://learn.microsoft.com/en-us/dotnet/api/system.func-2) on the official Microsoft documentation and Google.

We will reuse the same previous example as we now understand how to specify a return type for created method.

```csharp
// @nuget: Z.Expressions.Eval
using Z.Expressions;

// NOTE: The returned list contains "3" and "4"
var compiled = Eval.Compile<Func<List<int>>>(@"
var list = new List<int>() { 1, 2, 3, 4 };
return list.Where(x => x > 2).ToList();
");

var list = compiled();
```

{% include component-try-it.html href='https://dotnetfiddle.net/VnYJP7' %}

## Compile a C# expression with variable names

With the 2 previous topics, you should now be able to compile an expression and understand how to return the type of the compiled method.

However, almost every time, the compiled method will also have parameters.

There are multiple ways to pass variable names, such as:

- Position
- Named variables
- ExpandoObject

In the following example, we will see all the different ways to pass variable names that will be used in our expression to compile.

```csharp
// @nuget: Z.Expressions.Eval
using Z.Expressions;

var list = new List<int>() { 1, 2, 3, 4 };
var greaterThan = 2;

// Passing parameters with position
// NOTE: In this example, {0} is the first `List<int>` and {1} is the `int`
{
	var compiled = Eval.Compile<Func<List<int>, int, List<int>>>("{0}.Where(x => x > {1})");
	var rList = compiled(list, greaterThan);
	FiddleHelper.WriteTable((IEnumerable<int>)rList);
}

// Passing parameters with named variables
// NOTE: You need to name as many variables as the `Func` have (without counting the return type)
{
	var compiled = Eval.Compile<Func<List<int>, int, List<int>>>("list.Where(x => x > greaterThan)", "list", "greaterThan");
	var rList = compiled(list, greaterThan);
	FiddleHelper.WriteTable((IEnumerable<int>)rList);
}

// Passing parameters with an ExpandoObject
// NOTE: More flexible than the 2 previous examples but less robust as the library assume any missing name is part of the ExpandoObject
// NOTE: We had to cast the list to `(List<int>)` as until the method is executed, the library doesn't know which type is the variable `list`
{
	dynamic expandoObject = new ExpandoObject();
	expandoObject.list = list;
	expandoObject.greaterThan= greaterThan;
	
	var compiled = Eval.Compile<Func<ExpandoObject, List<int>>>("((List<int>)list).Where(x => x > greaterThan)");
	var rList = compiled(expandoObject);
	FiddleHelper.WriteTable((IEnumerable<int>)rList);
}
```

{% include component-try-it.html href='https://dotnetfiddle.net/LyRWFu' %} 

## Compile a C# expression from a string

Our library adds syntactic sugar to extend the `string` type with the `Execute` method. As you can expect, our library also extends the `string` type with the `Compile` method.

There is no difference here between calling `Eval.Compile` and `"the_string_expression".Compile`. Both call the same method under the hood.

In this example, we will compile an expression and loop to show only items in our list that currently have a higher value than the current position in our loop.

```csharp
// @nuget: Z.Expressions.Eval
using Z.Expressions;

var list = new List<int>() { 1, 2, 3, 4 };

var expression = "list.Where(x => x > i)";

var compiled = expression.Compile<Func<List<int>, int, List<int>>>("list", "i");
// SIMILAR TO: list = Eval.Compile<Func<List<int>, int, List<int>>>(expression, "list", "i");		

for(int i = 0; i < 4; i++)
{
	var rList = compiled(list, i);
	FiddleHelper.WriteTable("i=" + i, rList);
}	
```

{% include component-try-it.html href='https://dotnetfiddle.net/IUgauw' %}

## Compile a C# expression from a context

All the `Compile` methods we learned in previous examples use the global `EvalContext`. You can learn more about why and when you should use an instance context in the [EvalContext](/eval-context) documentation.

In this example, we will create an instance context, add a new extension method named `GreaterThan` and use the `Compile` method. Then after we will loop to show only items in our list that currently have a higher value than the current position in our loop.

```csharp
// @nuget: Z.Expressions.Eval
using Z.Expressions;

var list = new List<int>() { 1, 2, 3, 4 };		
var context = new EvalContext();

context.AddMethod(@"
bool GreaterThan(this int x, int y)
{
	return x > y;
}
");

var compiled = context.Compile<Func<List<int>, int, List<int>>>("list.Where(x => x.GreaterThan(i))", "list", "i");	

for(int i = 0; i < 4; i++)
{
	var rList = compiled(list, i);
	FiddleHelper.WriteTable("i=" + i, rList);
}	
```

{% include component-try-it.html href='https://dotnetfiddle.net/9ZE6nV' %}

The method `GreaterThan` only exists for this context. That means the `Eval.Compile` is unaware and cannot use this method.

## Summary

In this getting started tutorial, you learned how to use the `Compile` method with an expression to return a compiled method.

Using the [Compile](/eval-compile) method is a little bit more complex than the [Execute](eval-execute) method to understand. However, make sure you take the time to really learn it, as it is an essential method to master to make sure you take advantage of the performance benefits this method offers.