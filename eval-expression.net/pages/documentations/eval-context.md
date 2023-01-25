---
Name: Eval Context
---

# Eval Context

The `EvalContext` is the main class of the C# Eval Expression library. In a few words, the `EvalContext` is the context under which an evaluation or compilation is executed.

You can customize the `EvalContext` through:

- [Options](/options)
- [Register / Unregister Methods](/register-unregister)
- [Internal Dictionary](/internal-dictionary)

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

## Conclusion

There is no best unique way to use an `EvalContext` as they are all good solutions depending on your scenario. They are quite easy to learn, so make sure to understand them correctly to best use our library.

## TODO:

But what is the global context? That is simply an instance of a [EvalContext](/eval-context), but stored in a static variable in the [EvalManager](/eval-manager). So both methods call under the hood the same method, but one takes the "code" as a parameter, and the other extends the string type, so the "code".