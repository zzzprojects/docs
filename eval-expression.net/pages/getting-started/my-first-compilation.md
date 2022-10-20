---
Title: Compile C# Code at Runtime - Get Started with your First Compilation
MetaDescription: Learn how to compile a C# expression at runtime with parameter, return type, and from a dynamic string expression.
---

# My First Compilation

In this tutorial, we will assume that you have read and understood the concept we introduced in the [My First Evaluation](/my-first-evaluation) tutorial. If you have not read it, make sure you start with this previous tutorial, as we might skip the explanation of a few concepts.

So what is the major difference between executing and compiling a dynamic code?

- The `Compile` method will compile the expression into a method
- The `Execute` method call the `Compile` method under the hood and then execute the returned compiled method.

When executing the same expression in the `Execute` method multiple times, it is fast as we retrieve the compiled from the cache. However, retrieving the compiled method from the cache still takes some time, as we need to create a cache key that has a performance cost. So when you need to execute multiple times the same expression, you should store the compiled method in a static or local variable.

How much faster? The `Compile` method can be 1000x more quickly than the `Execute` method. It doesn't make any difference when looping on ten items, but when looping on millions of items, it makes a massive difference between a few milliseconds and seconds.

{% include component-try-it.html href='https://dotnetfiddle.net/W2qpBX' %}

In this tutorial, you will learn how to use our library to:

- [Compile a C# expression](#compile-a-csharp-expression)
- [Compile a C# expression with return type](#compile-a-csharp-expression-with-return-type)
- [Compile a C# expression with variable names](#compile-a-csharp-expression-with-variable-names)
- [Compile a C# expression from a string](#compile-a-csharp-expression-from-a-string)
- [Compile a C# expression from a context](#compile-a-csharp-expression-from-a-context)

## Compile a C# expression

Like the `Execute` method, the `Compile` method takes as the first parameter the expression to compile.

In this example, we will dynamically create a list of `int` and filter it using LINQ to return only items greater than 2. The expression will be compiled into a method and then executed. The `Func<List<int>>` return type" will be explained in the next example.

```csharp
// NOTE: The returned list contains "3" and "4"
var compiled = Eval.Compile<Func<List<int>>>(@"
var list = new List<int>() { 1, 2, 3, 4 };
return list.Where(x => x > 2).ToList();
");

var list = compiled();
```

{% include component-try-it.html href='https://dotnetfiddle.net/VnYJP7' %}  

## Compile a C# expression with return type

In the last example, we successfully compiled an expression, but what exactly was the `Func<List<int>>`?

The `Func<List<int>>` means that the `Compile` method should create a method that takes no parameter and returns a `List<int>`. Usually, the `Compile` method takes either an `Action` or `Func`. A `Func` should be used when the compiled method should return a specific type and an `Action` should be used when the method doesn't return any type (like when you create a `void` method).

If you don't know what is an `Action` and `Func` work, you can learn more using Google.

That is the same example as the previous one. As we will learn how to use parameters in the next topic:

```csharp
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

However, almost every time, the compiled method would have parameters.

There are multiple ways to pass variable names, such as:

- Position
- Named variables
- ExpandoObject

In the following example, we will see all the different ways to pass variable names.

```csharp
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

Our library added syntactic sugar to extend string variables with the `Execute` method but also with the `Compile` method.

There is no difference here between calling `Eval.Compile` and `"mystring".Compile`. Both call the same method under the hood.

In this example, we will compile an expression and loop show only items in our list that currently have a higher value than the current position in our loop.

```csharp
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

{% include component-try-it.html href='https://dotnetfiddle.net/https://dotnetfiddle.net/IUgauw' %}

## Compile a C# expression from a context

All the `Compile` methods we learned in previous examples use the global `EvalContext` stored in the `EvalManager.DefaultContext`. In other words:

- using the `Eval.Compile` method is similar to doing `EvalManager.DefaultContext.Compile`.
- using the `"the_string_expression".Compile` method is similar to doing `EvalManager.DefaultContext.Compile`.

In this example, we will create an instance context, add a new extension method named `GreaterThan` and use the `Compile` method. Then after we will iterator loop to show only item that are greater then the current loop position.

```
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

## Conclusion

In this getting started tutorial, you learned how to use the `Compile` method with an expression to return a compiled method.

Using the `Compile` method is a little bit more complex than the `Execute` method to understand but make sure you take the time to really understand it as this is an essential method to master to make sure you take advantage of the performance benefit this method offers.