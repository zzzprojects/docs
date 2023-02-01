---
Name: Eval Context
---

# What is the Eval Context?

The `EvalContext` is the main class of the C# Eval Expression library. No matter how you call the [Execute](/eval-execute) or [Compile](/eval-compile) methods, an `EvalContext` is used.

There is 2 ways to use an `EvalContext`:
1. Creating a new instance of the `EvalContext` class
2. Using the global context

## 1. Creating a new instance of the EvalContext class

## 2. Use the global context

The global context is used when calling `Execute` and `Compile` methods not from a context such as:

- `Eval.Execute(code)`
- `"code".Execute()`
- `linq.Execute(code)`

In this 3 examples, no context was directly used but in fact, under the hood, all thoses method called the `Execute` method from the `EvalManager`.

## EvalContext customization

You can customize the local and global `EvalContext` by using:

1. [Options](/options)
2. [Register / Unregister Methods](/register-unregister)

### 1. Options

Learn more about [Options](/options)

### 2. Register / Unregister Methods

Learn more about [Register / Unregister Methods](/register-unregister)

## Conclusion

There is no best unique way to use an `EvalContext` as they are all good solutions depending on your scenario. They are quite easy to learn, so make sure to understand them correctly to best use our library.

---



## What's the difference between an "instance" and a "global" context?

We often talk about the global context through our documentation, but the explanation might not always be clear until you reach this article.

An **instance context** is when you create a new instance of the [EvalContext](/eval-context), and then you can use the `Execute` method from the context created, such as:

```csharp
// TODO
```

The **global context** is used when you call methods such as:

- `Eval.Execute` or `Eval.Compile`
- `"code".Execute` or `"code".Compile`
- [LINQ Dynamic Methods](/linq-dynamic)

Under the hood, those methods will use the `DefaultContext`. That is a static property of the [EvalManager](/eval-manager).

In other words, doing `Eval.Execute` is equivalent to doing `EvalManager.DefaultContext.Execute`:

```csharp
// TODO
```

Methods such as `Execute` and `Compile` are thread-safe, meaning there is no problem calling them in a concurrency scenario with the **global context**. However, configuring the **global context** should only be done when starting your application, as configuration impact how the compilation is made, so that's not thread-safe.









when you create a new instance of the [EvalContext](/eval-context), and then you can use the `Execute` method from the context created, such as:


Difference between an EvalContext instance and the Global Context?

Methods such as:



Use a global context under the hood. The global context is the 

In other words, using `Eval.Execute` is equivalent of doing `EvalManager.DefaultContext.Execute`




There are 3 ways to use an `EvalContext`:

- Use the global EvalContext instance
- Use a new EvalContext instance
- Use a custom global EvalContext instance

## Use the global EvalContext instance

Using the global `EvalContext` from the [Eval Manager](/eval-manager), is the easiest way to get started with our library.

Under the hood, methods such as `Eval.Execute`, Eval.Compile`, `String.Execute`, `String.Compile` and LINQ method use it by default.

The global `EvalContext` instance should only be customized by the options you want to apply globally. A rule of thumb is if an evaluation needs an option that cannot be set when the application starts, then this evaluation should not use the global `EvalContext` instance.

```csharp
```

## Use a new EvalContext instance

Creating a new `EvalContext` instance is often used in a method with some options that are passed in parameters such as:

```csharp
```

Since those options cannot be set when the application starts, and they are not global but specific for this evaluation only, it makes sense to create a new context in this case.

## Use a custom global EvalContext instance

Using a custom global `EvalContext` instance is a mixte of both previous cases. For example, let's say you already know that sometimes you need to evaluate an expression in [safe mode ](/safe-mode), but you don't want these options to be in your global `EvalContext` instance.

Instead of re-creating the same instance again and again in every method that needs this configuration, you can create a static class with a static context already configured for this purpose:

```csharp
```

It makes the code in your method more readable and makes it easier to change configuration globally.


## Can I create a custom global context?
Yes



## TODO:

But what is the global context? That is simply an instance of a [EvalContext](/eval-context), but stored in a static variable in the [EvalManager](/eval-manager). So both methods call under the hood the same method, but one takes the "code" as a parameter, and the other extends the string type, so the "code".

