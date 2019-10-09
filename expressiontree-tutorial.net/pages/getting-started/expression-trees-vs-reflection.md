# Expression Trees vs Reflection

## Reflection

Reflection provides objects of type `System.Type` that describe assemblies, modules and types. It allows you to create an instance of a type, and bind it to an existing object, or get the type from an existing object and invoke its methods or access its fields and properties. 

You can use `GetType` method to obtain the type of a variable.

```csharp
// Using GetType to obtain type information:  
int number = 60;
string str = "Expression Trees vs Reflection";
double doubleVal = 12.5;

Type numType = number.GetType();
Type strType = str.GetType();
Type doubleType = doubleVal.GetType();
```

Expression Trees vs Reflection

In .NET, It is commonly known that reflection slow, but why is that the case?

*****//It is still in progress******