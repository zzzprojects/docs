# My First Evaluation

C# is a powerful programming language but lacks the **eval** method that some other languages have, such as JavaScript.

The C# Eval Expression library is the best solution to overcome this limitation and lets you easily evaluate code at runtime with the minimum code required on your side.

In this tutorial, you will learn how to use our library with a:

Evaluate a C# expression
Evaluate a C# expression with variables
Evaluate a C# expression with return type
Evaluate a C# expression from a context
Evaluate a C# expression from a string

## Evaluate a C# expression

The simplest way to execute a C# expression at runtime is by using the `Eval.Execute` method. Under the hood, it calls the `Execute` method from the `DefaultContext` that you will learn later in this tutorial.

The `Execute` method takes as the first parameter the code to execute. 

In this example, we will dynamically create a list of `int` and filter it using LINQ to return only items that are greater than 2. 

```csharp
// the final list will contain only the item '3' and '4'
var list = Eval.Execute(@"
var list = new List<int>() { 1, 2, 3, 4 };
return list.Where(x => x > 2).ToList();
");
```

Remember that the C# Eval library supports way more than this basic statement. However, we will keep it very simple in our getting started section.

## Evaluate a C# expression with variables

In the last example, we created the list dynamically in the `Execute` method and the value `2`, which we used to filter, but what if those variables already existed?

The `Execute` method takes the second parameter (or all remaining parameters) variables to use in our expression.

There are multiple different ways of passing parameters, such as:
Anonymous Type
Class Member
Dictionary
Expando Object
Values

In this example, we will create a list and a greaterThan variable and show different ways to pass this variable to evaluate the expression.

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

Now we learned the base of how to specify an expression and parameters, the latest part to see is how to choose a return type.

You can use a generic type from a method to choose a return type. If none is provided, by default the type `object` is used as our library doesn't know exactly which type you are expecting. 

```csharp
```

## Evaluate a C# expression from a context

## Evaluate a C# expression from a string

In addition of bing able to execute from a `static context` and `instance context`, you can execute a c# expression directly from a string

```csharp
// ...code...
```

This method is a syntactic sugar syntax because under the hood, the `EvalManager.DefaultContext.Execute` method is called.

## Conclusion