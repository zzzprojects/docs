# Field

You can build an expression tree which contains accessing a given field by its name using `Expression.Field` method. For example, you have the following code.

```csharp
TestClass obj = new TestClass();
Console.WriteLine(obj.myField);
```

Here is the code that is required to build the same functionality using expression tree. 

```csharp
Expression fieldExpr = Expression.Field(
    Expression.Constant(obj),
    "myField"
);

Console.WriteLine(Expression.Lambda<Func<int>>(fieldExpr).Compile()());
```

[Try it online](https://dotnetfiddle.net/SRpc98)