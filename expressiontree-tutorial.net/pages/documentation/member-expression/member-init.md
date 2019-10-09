# MemberInit

You can build an expression tree which contains creating of a new object and initializes a property of the object using `Expression.MemberInit` method. For example, you have the following code.

```csharp
Employee e = new Employee()
{
    Name = "John",
    BirthDate = DateTime.Now
};

Console.WriteLine("Name {0}, DOB {1}",e.Name, e.BirthDate);
```

Here is the code that is required to build the same functionality using expression tree. 

```csharp
Expression testExpr = Expression.MemberInit(
    Expression.New(typeof(Employee)),
    new List<MemberBinding>() {
        Expression.Bind(typeof(Employee).GetMember("Name")[0], Expression.Constant("John")),
        Expression.Bind(typeof(Employee).GetMember("BirthDate")[0], Expression.Constant(DateTime.Now))
    }
);

var emp = Expression.Lambda<Func<Employee>>(testExpr).Compile()();
Console.WriteLine("Name {0}, DOB {1}",emp.Name, emp.BirthDate);
```

[Try it online](https://dotnetfiddle.net/FrLceJ)