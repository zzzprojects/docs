# Property

You can build an expression tree which contains accessing a given property by its name using `Expression.Property` method. For example, you have the following code.

```csharp
TestClass obj = new TestClass();
obj.myProperty = 60;
Console.WriteLine(obj.myProperty);
```

Here is the code that is required to build the same functionality using expression tree. 

```csharp
Expression propertyExpr = Expression.Property(
    Expression.Constant(obj),
    "myProperty"
);

Console.WriteLine(Expression.Lambda<Func<int>>(propertyExpr).Compile()());
```

[Try it online](https://dotnetfiddle.net/OjQ63U)