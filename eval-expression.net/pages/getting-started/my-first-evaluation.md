---
Name: My First Evaluation
---

# My First Evaluation

C# is a powerful programming language but lacks the "eval" method that some other languages, such as JavaScript, provide.

In this tutorial, you will learn how to overcome this limitation by being able to evaluate code at runtime using our C# Eval Expression library.

Summary:
Simple C# Expression
Simple C# Expression with parameters
Simple C# Expression with a return type
Simple C# Expression from a context
Simple C# Expression from a string
Advanced C# Expression

## 1. Simple C# Expression

The simplest way to execute a C# expression at runtime is by using the `Eval.Execute` method.

The code in the first parameter is parsed, compiled, and then executed.

```csharp
// ...code...
```

## 2. Simple C# Expression with parameters

In addition to the expression, you can also pass parameters to be used later. Multiple different way of passing parameters is supported such as:
Anonymous Type
Class Member
Dictionary
Values

```csharp
// ...code...
```

One of the main features of our library is being able to access class members/dictionary values directly

```csharp
// ...code...
```

## 3. Simple C# Expression with a return type

Now we learned the base of how to specify an expression and parameters, the latest part to see is how to choose a return type.

You can use a generic type from a method to choose a return type. If none is provided, by default the type `object` is used as our library doesn't know exactly which type you are expecting. 

```csharp
```

## 4. Simple C# Expression from a context

## 5. Simple C# Expression from a string

In addition of bing able to execute from a `static context` and `instance context`, you can execute a c# expression directly from a string

```csharp
// ...code...
```

This method is a syntactic sugar syntax because under the hood, the `EvalManager.DefaultContext.Execute` method is called.

## 6. Advanced C# Expression

We have seen very basic usage in our library, but obviously, our C# Eval Expression library is way more powerful than this.

Pretty much all the C# Language is supported:
... keywords
... extension methods

What might be important here is to understand about RegisterType...