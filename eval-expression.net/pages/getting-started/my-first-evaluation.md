---
Title: Execute C# Code at Runtime - Get Started with your First Evaluation
MetaDescription: Learn how to execute a C# expression at runtime with parameter, return type, and from a dynamic string expression.
---

_Documentation in progress_

# My First Evaluation

C# is a powerful programming language but lacks the **eval** method that some other languages have, such as JavaScript.

The C# Eval Expression library is the best solution to overcome this limitation and lets you easily evaluate code at runtime with the minimum code required on your side.

In this tutorial, you will learn how to use our library to:

- [Evaluate a C# expression](#)
- [Evaluate a C# expression with variables](#)
- [Evaluate a C# expression with return type](#)
- [Evaluate a C# expression from a string](#)
- [Evaluate a C# expression from a context](#)

## Evaluate a C# expression

The simplest way to execute a C# expression at runtime is by using the `Eval.Execute` method. Under the hood, it calls the `Execute` method from the `DefaultContext` that you will learn later in this tutorial.

The `Execute` method takes as the first parameter the code to execute. 

In this example, we will dynamically create a list of `int` and filter it using LINQ to return only items greater than 2. 

```csharp
// NOTE: The returned list contains only the item `3` and `4`
var list = Eval.Execute(@"
var list = new List<int>() { 1, 2, 3, 4 };
return list.Where(x => x > 2).ToList();
");
```

Remember that the C# Eval library supports more than this basic statement. However, we will keep it very simple in our getting started section.

## Evaluate a C# expression with variables

In the last example, we created the list dynamically in the `Execute` method and hardcoded the value `2`, which we used to filter. However, in most real-life scenarios, those values will be from variables.

The `Execute` method takes the variables to use in our expression (or all remaining parameters) as the second parameter.

There are multiple different ways of passing variables, such as:

- Anonymous Type
- Class Member
- Dictionary
- Expando Object
- Values

In this example, we will create the `list` and `greaterThan` variables and show different ways to pass those variables to evaluate the expression.

```csharp
var list = new List<int>() { 1, 2, 3, 4 };
var greaterThan = 2;

// Anonymous
listAnonymous = Eval.Execute("list.Where(x => x > greaterThan)", new { list, greaterThan });

// Anonymous + Named Member
listAnonymousNamedMember = Eval.Execute("listName.Where(x => x > greaterThanName)", new { listName = list, greaterThanName = greaterThan });

// Class Member (when only using 1 parameter, you can directly use member name)
listAnonymousNamedMember = Eval.Execute("Item1.Where(x => x > Item2)", new Tuple<List<int>, int>(list, greaterThan));

// Dictionary (when only using 1 parameter, you can directly use key name)
var dictionary = new Dictionary<string, object>();
dictionary.Add("list", list);
dictionary.Add("greaterThan", greaterThan);
listAnonymousNamedMember = Eval.Execute("list.Where(x => x > greaterThan)", dictionary);

// Expando Object (when only using 1 parameter, you can directly use member name)
dynamic expandoObject = new ExpandoObject();
var dictionary = new Dictionary<string, object>();
expandoObject.List = list;
expandoObject.GreaterThan= greaterThan;
listAnonymousNamedMember = Eval.Execute("list.Where(x => x > greaterThan)", dictionary);

// Values (since they are not named, you need to use position
listAnonymousNamedMember = Eval.Execute("{0}.Where(x => x > {1})", list, greaterThan);
```

## Evaluate a C# expression with a return type

In the last two examples, we learned how to execute code dynamically and use variables in our expressions.

The last remaining part is how to specify a return type. The value will be from type `object` if no return type is specified.

The `Execute` method takes as the generic type the type to return.

In this example, we will create the `list` and `greaterThan` variables and use our getting started expression but this time, specify the `List<int>` return type.

```csharp
var list = new List<int>() { 1, 2, 3, 4 };
var greaterThan = 2;

list = Eval.Execute<List<int>>("list.Where(x => x > greaterThan)", new { list, greaterThan });
```

Did you spot the error in this example? The expression should return an `IEnumerable<int>` and not a `List<int>`, but our library is smart enough to make logic to make your life easier and automatically call the `ToList` method at your place. That is one of the many advantages of using our library.

## Evaluate a C# expression from a string

If you have taken the time to read and understand the three first sections of this tutorial, you are probably already all set to begin your journey and learn more about our library.

Our library adds a syntactic sugar execute extension method to extend your string.

In that example, we will execute an expression directly from a string variable instead.

```csharp
var list = new List<int>() { 1, 2, 3, 4 };
var greaterThan = 2;
var expressionToExecute = "list.Where(x => x > greaterThan)";

list = expressionToExecute.Execute<List<int>>(new { list, greaterThan });
```

Under the hood, we call the same method as `Eval.Execute` and set the string as the first parameter.

## Evaluate a C# expression from a context

All the `Execute` methods we learned in previous examples use the global `EvalContext` stored in the `EvalManager.DefaultContext`. In other words, using the `Eval.Execute` method is similar to doing `EvalManager.DefaultContext.Execute`.

So anyway, what exactly is an `EvalContext`? That is a class you instancies to specify some configuration. You can set configuration globally by using `EvalManager.DefaultContext` or storing your context in a static variable you re-use later or by simply creating a context inside a method.

In this example, we will create an instance context and call the `Execute` method. We will also use a method that we will create dynamically.

```
context...

AddMethod

Usemethod
```

The method `CustomFilter` only exists for this context. That means the `Eval.Execute` is unaware and cannot use this method.

## Conclusion

In this getting started tutorial, you learned how to use the `Execute` method to dynamically evaluate some code, use parameters, and a return type.

Using the `Execute` method is straightforward, and most developers should already be able to use it without a problem. However, only time will allow you to master this method and exploit all the potential through all options of the `EvalContext` and the possibility that the library offers, so make sure to continue to read other tutorials and articles.

We recommend registering to our [newsletter](https://mailchi.mp/zzzprojects/eval_expression_newsletter) to stay updated with the latest released documentation.