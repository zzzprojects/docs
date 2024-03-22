---
Name: Helper methods
LastMod: 2024-03-22
---

# Helper Methods

List

- AddMethod
- Tokenize

## AddMehod

As the name specifies, the `AddMethod` allow you to add a method or extension method to the context. Then you can use this method to evaluate or compile your C# expression.


For example, let's add a C# method that will add 2 numbers:

```csharp

```

Let's take another example and assume that you let your user filter a report, but one of your users would like to filter by age. Obviously, he can already do it through our library, but you could make is life easier by adding an extension method that will calculate the age from a date:

```csharp
```

The `AddMethod` is very flexible and powerful as it lets you add some methods to make some C# evaluations easier by adding some `helper` methods.

## Tokenize

The Tokenize method parses an expression and returns a list of TokenInfo. The method can be helpful to understand better how the library parses an expression or to extract some specific part, such as finding all string tokens.

For example, the following expression:

```csharp
```

After tokenizing will return the following tokens:

```tbd
```



