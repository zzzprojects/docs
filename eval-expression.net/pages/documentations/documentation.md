---
Name: Documentation
LastMod: 2024-03-22
---

# Documentation

Are you using the C# Eval Expression Library for a long time, or are you currently evaluating it and want to try it? In both cases, reading that documentation and coming back from time to time is very important to ensure you can exploit our library's full potential with every one of your projects.

## Methods

Our C# expression evaluator can be split into 3 categories of methods:

- [Execute Methods](/eval-execute)
- [Compile Methods](/eval-compile)
- [Helper Methods](/helper-methods)

Execute methods is the most critical section to understand as they allow you to execute an expression directly, such as:

```csharp
```

Compile methods are also very important to take your time to read the documentation as they allow you to compile a C# expression and re-use the delegate later:

```csharp
```

Helper methods are different kinds of methods that either can help you for other purposes, such as the `Tokenize` method, or help you with your current evaluation, such as the `AddMethod` method:

```csharp
```

### LINQ Dynamic Methods

Since the introduction of LINQ methods, they have become a critical part of our day-to-day coding. Methods such as `Where` or `Order By` are used in every C# project nowadays. However, there is still a significant gap that still needs to be filled, which is being able to use LINQ methods with dynamic expressions:

- [LINQ Dynamic Methods](/linq-dynamic)          

```csharp
```

## Eval Context

In this section, you will better understand when to use and which methods use the global `EvalContext`. You will also learn when to create an instance of an `EvalContext`:

- [EvalContext](/eval-context)

## Eval Manager

In this section, you will learn about the static EvalManager and what it's for:

- [EvalManager](/eval-manager)

## Options

The C# Eval library is very flexible and allows you to customize the evaluation of an expression. For example, it allows you to choose if members are case sensitives, the maximum allowed loop, and more.

- [Options](/options)    

```csharp
```

## Register / Unregister / IsRegistered Methods

Understanding register methods is essential as, by default, our library is unaware of existing methods from your project or referenced DLL:

- [Register Methods](/register-unregister#register-methods)
- [Unregister Methods](/register-unregister#unregister-methods)
- [IsRegistered Methods](/register-unregister#isregistered-methods)

## Internal Dictionary

We do not recommend playing with what we consider `internal` dictionary. Still, since those dictionaries are `public` as some people have complex scenarios that are the only way to make it works, they also deserve their part in our documentation:

- [Internal Dictionary](/internal-dictionary)