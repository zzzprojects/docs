---
PermaID: 101006
Name: Create Data Classes Dynamically
---
# Create Data Classes Dynamically

A data class is a class that contains only data members. The `System.Linq.Dynamic.DynamicExpression` class defines the following methods for dynamically creating data classes.

```csharp
public static Type CreateClass(params DynamicProperty[] properties);
public static Type CreateClass(IEnumerable<DynamicProperty> properties);
```

The `CreateClass` method creates a new data class with a given set of public properties and returns the `System.Type` object for the newly created class. 

 - If a data class with an identical sequence of properties has already been created, the `System.Type` object for this class is returned.
 - Data classes implement private instance variables and read/write property accessors for the specified properties. 
 - Data classes also override the `Equals` and `GetHashCode` members to implement by-value equality.

Here is a simple example of a dynamically created data class.

```csharp
DynamicProperty[] props = new DynamicProperty[]
{
    new DynamicProperty("Name", typeof(string)),
    new DynamicProperty("Birthday", typeof(DateTime))
};

Type type = System.Linq.Dynamic.DynamicExpression.CreateClass(props);

object obj = Activator.CreateInstance(type);

type.GetProperty("Name").SetValue(obj, "Albert", null);
type.GetProperty("Birthday").SetValue(obj, new DateTime(1879, 3, 14), null);

Console.WriteLine(obj);
```

The above code snippet uses the `CreateClass` method to create a data class with two properties, `Name` and `Birthday`, and then uses .NET reflection to create an instance of the class and assign values to the properties.

## How does it work?

Data classes are created in an in-memory assembly in the current application domain. All data classes inherit from `System.Linq.Dynamic.DynamicClass` and are given automatically generated names that should be considered private (the names will be unique within the application domain but not across multiple invocations of the application). 

 - Once a data class is created, it stays in memory for the lifetime of the current application domain. 
 - There is currently no way to unload a dynamically created data class.
 - The dynamic expression parser uses the `CreateClass` methods to generate classes from data object initializers. 
 - This feature is often used with the dynamic Select method to create projections.
