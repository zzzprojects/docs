# PropertyOrField

You can build an expression tree which contains accessing a given property or field by its name using `Expression.PropertyOrField` method. For example, you have the following code.

```csharp
TestClass obj = new TestClass();
obj.myProperty = 60;

Console.WriteLine("Property: {0}",obj.myProperty);
Console.WriteLine("Field: {0}",obj.myField);
```

Here is the code that is required to build the same functionality using expression tree. 

```csharp
Expression propertyExpr = Expression.PropertyOrField(
    Expression.Constant(obj),
    "myProperty"
);

Expression fieldExpr = Expression.PropertyOrField(
    Expression.Constant(obj),
    "myField"
);

Console.WriteLine("Property: {0}",Expression.Lambda<Func<int>>(propertyExpr).Compile()());
Console.WriteLine("Field: {0}",Expression.Lambda<Func<int>>(fieldExpr).Compile()());
```

[Try it online](https://dotnetfiddle.net/JgeN3p)