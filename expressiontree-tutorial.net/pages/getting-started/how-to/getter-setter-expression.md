# Getter/Setter Expression

Properties enable a class to expose a public way of getting and setting values, while hiding implementation or verification code.

 - A get property accessor is used to return the property value.
 - And a set property accessor is used to assign a new value. 

You can also get/set the property using Expression API. Here is a simple `Employee` class which contains two properties.

```csharp
public class Employee
{
    public string Name { get; set; }
    public DateTime BirthDate { get; set; }
}
```

The following class contains implementation for getter/setter using expression APIs

```csharp
public static class ExpressionUtils
{
    public static Action<TEntity, TProperty> CreateSetter<TEntity, TProperty>(string name) where TEntity: class
    {
        PropertyInfo propertyInfo = typeof(TEntity).GetProperty(name);

        ParameterExpression instance = Expression.Parameter(typeof(TEntity), "instance");
        ParameterExpression propertyValue = Expression.Parameter(typeof(TProperty), "propertyValue");

	var body = Expression.Assign(Expression.Property(instance, name), propertyValue);

        return Expression.Lambda<Action<TEntity, TProperty>>(body, instance, propertyValue).Compile();
    }

    public static Func<TEntity, TProperty> CreateGetter<TEntity, TProperty>(string name) where TEntity: class
    {
        ParameterExpression instance = Expression.Parameter(typeof(TEntity), "instance");

        var body = Expression.Property(instance, name);

        return Expression.Lambda<Func<TEntity, TProperty>>(body, instance).Compile();
    }
}
```
## Setter

You can set the property value of a specified object by using the `CreateSetter` method.

```csharp
var setterNameProperty = ExpressionUtils.CreateSetter<Employee, string>("Name");
var setterBirthDateProperty = ExpressionUtils.CreateSetter<Employee, DateTime>("BirthDate");

Employee emp = new Employee();

setterNameProperty(emp, "John");
setterBirthDateProperty(emp, new DateTime(1990, 6, 5));

Console.WriteLine("Name: {0}, DOB: {1}", emp.Name, emp.BirthDate);
```

## Getter

You can get the property value of a specified object by using the `CreateGetter` method.

```csharp
var getterNameProperty = ExpressionUtils.CreateGetter<Employee, string>("Name");
var getterBirthDateProperty = ExpressionUtils.CreateGetter<Employee, DateTime>("BirthDate");

Employee emp1 = new Employee()
{
    Name = "John",
    BirthDate = new DateTime(1990, 6, 5)
};

var name = getterNameProperty(emp1);
var birthDate = getterBirthDateProperty(emp1);

Console.WriteLine("Name: {0}, DOB: {1}", name, birthDate);
```

[Try it online](https://dotnetfiddle.net/IiTlLS)
