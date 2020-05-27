---
Permalink: advanced-create-dynamic-class
Name: Create Dynamic Class
---

# Create Dynamic Class

A data class is a class that contains only data members. The static `System.Linq.Dynamic.Core.DynamicClassFactory` class defines the following method for dynamically creating a type.

``` csharp
public static Type CreateType([NotNull] IList<DynamicProperty> properties, bool createParameterCtor = true)
```

## CreateType

The `CreateType` method creates a new data class with a given set of public properties, and it returns the `System.Type` object for the newly created class.

- If a data class with an identical sequence of properties has already been created, the `System.Type` object for this class is returned.
- Data classes implement private instance variables and read/write property accessors for the specified properties.
- Data classes also override the `Equals` and `GetHashCode` members to implement by-value equality.
- Data classes are created in an in-memory assembly in the current application domain.
- All data classes inherit from `System.Linq.Dynamic.Core.DynamicClass` and are given automatically generated names that should be considered private (the names will be unique within the application domain but not across multiple invocations of the application). Note that once created, a data class stays in memory for the lifetime of the current application domain. There is currently no way to unload a dynamically created data class.
- Optionally, you can define if you want to generate a constructor with parameters. Default set to true. Note that for Linq-to-Database objects, this needs to be set to false.

## Example

Here is a simple example of how to use the DynamicClassFactory to create a dynamically created Type with two properties, `Name` and `Birthday` and then uses .NET reflection to create an instance of the class and assign values to the properties.

```csharp
var props = new DynamicProperty[]
{
    new DynamicProperty("Name", typeof(string)),
    new DynamicProperty("Birthday", typeof(DateTime)) 
};

Type type = DynamicClassFactory.CreateType(props);

var dynamicClass = Activator.CreateInstance(type) as DynamicClass;
dynamicClass.SetDynamicPropertyValue("Name", "Albert");
dynamicClass.SetDynamicPropertyValue("Birthday", new DateTime(1879, 3, 14));

// Use the class here ...
Console.WriteLine(dynamicClass);
```

[Try it online](https://dotnetfiddle.net/B5rIoN)
