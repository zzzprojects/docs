---
Name: Eval Context
LastMod: 2024-03-22
---

# What is the Eval Context?

The `EvalContext` is the main class of the C# Eval Expression library. No matter how you call the [Execute](/eval-execute) or [Compile](/eval-compile) methods, an `EvalContext` is used.

There are 2 ways to use an `EvalContext`:

- **Instance Context:** When you create a new instance of the `EvalContext` class
- **Global Context:** When you use a method that uses under the hood the `EvalManager.DefaultContext`.

## Instance Context

The instance context is the common usage you know from a class. You create a new instance of the `EvalContext` and directly use properties and methods from it:

```csharp
// CREATE a new instance of EvalContext
var context = new EvalContext();

// USE the `Execute` method from the context created
var list1 = context.Execute<List<int>>("new List<int>() { 1, 2, 3 };");

FiddleHelper.WriteTable(list1);
```

{% include component-try-it.html href='https://dotnetfiddle.net/3ykFkn' %}

## Global Context

The global context is an instance stored in a static variable in the [EvalManager](/eval-manager) (`EvalManager.DefaultContext`).

We call it the global context because:

- You can access this context anywhere in your code without creating an instance
- Multiple methods of our library not related to a context use this global context under the hood, such as:
   - `Eval.Execute(code)`
   - `"code".Execute()`
   - `linq.Execute(code)`

```csharp
var list2 = Eval.Execute<List<int>>("new List<int>() { 1, 2, 3 };");

var list3 = "new List<int>() { 1, 2, 3 };".Execute<List<int>>();
```

{% include component-try-it.html href='https://dotnetfiddle.net/YlT2Zc' %}

## How to customize a Context?
   
You can customize the instance and global context with

1. [Options](/options)
2. [Register / Unregister Methods](/register-unregister)

### 1. Options

Customizing your context with options allows you to change how an expression is parsed and executed. Here are a few examples:

- **IsCaseSensitive:** Allow you to set if properties and methods names in the expression should respect the casing
- **MaxLoopIteration:** Allow you to set a maximum of iterations to avoid an infinite loop
- **SafeMode:** Allow you to restrict the usage of our library to only what you want the user to do

See the [Options](/options) documentation for more information and examples.

### 2. Register / Unregister Methods

By default, a context doesn't know about types, variables, and constants in your project and referenced projects. So if you have a `Customer` type and when to use it in your expression, you must first register it.

Here is a short list of what you can register in the context:

- **Register a type**
- **Register a static method**
- **Register a constant**
- **Register a global variable**
- **Register a keyword**

See the [Register / Unregister Methods](/register-unregister) documentation for more information and examples.

## When to use an Instance Context vs. the Global Context?

The usual answer we provide is:

- **Instance Context:**  If you need to customize the context for this specific evaluation, **you should** use an instance context
- **Global Context:** If you don't need to customize the context for this specific evaluation, **you can** use the global context

All methods, options, and registers methods are thread-safe within the C# Eval Expression library, but being thread-safe doesn't mean you can necessarily use it in any way in a concurrency scenario.

For example, is a `ConcurrencyDictionary` thread-safe? Sure, but if `Thread A` adds a key and `Thread B` tries to remove the key if it exists, will the key still exist for `Thread A`? The same problem happens when you change options, register or unregister type.

Configuring the **global context** should only happen once, for example, when the application starts and never after. This is why we say:

- **You should:** use an instance context if you need to customize the context. Otherwise, it can lead to some side impact
- **You can:** use the global context if you don't need to customize the context. There is no problem with using an instance context either in this case

The #1 problem by far we see by people using this library is that they keep modifying the **global context** without thinking that it could impact another thread that will eventually compile an expression.

## Conclusion

The `EvalContext` is the class controlling almost all the logic in the C# Eval Expression library. This class allows you to:

- Set options
- Register type, variables, and constants
- Execute your code
- Compile your code
- And more

By itself, the `EvalContext` is very easy to use. The most question/problem we receive is when a developer modifies the global context when he should have created an instance context, so make sure to understand this section correctly.