---
Name: Register & Unregister Methods
LastMod: 2024-03-22
---

# How to use  Register & Unregister Methods in C# Eval Expression library

In the C# Eval Expression, the library is unaware of any namespace, class, method, or anything else besides what has been registered by default. Even if we included a lot of namespace and objects by default such as `System.Linq`, `DataTable`, and `Math` for most basic cases, that is often not enough, and you need to register or even unregister on your side what is missing.

## Register Methods

The `Register` methods allow you to add types, methods, properties, fields, values, and even keywords that you can use in the library. 

| Name                               | Description                                                                                                                                                                                                                                                                                                             |
| ---------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| RegisterAlias                      | The RegisterAlias method registers an alias for a variable, constant, method or type name. For example, if you register the alias "mymath" for the class "System.Math", you can now create an expression such as "mymath.Min(1, 2)".                                                                             |
| RegisterAssembly                   | The RegisterAssembly method register all types from the assemblies provided.                                                                                                                                                                                                                                             |
| RegisterAutoAddMissingTypeAssembly | The RegisterAutoAddMissingTypeAssembly method register assemblies in which the option \`AutoAddMissingTypes\` or \`RetryAndThrowMissingTypes\` is allowed to retrieve types.                                                                                                                                            |
| RegisterDefaultAliasSafe           | The RegisterDefaultAliasSafe method register types, members and extensions methods of all safe types consired safe (Array, Dictionary, List, Tuple, etc.). You can find the complete list here:                                                                                          |
| RegisterDefaultAliasUnsafe         | The RegisterDefaultAliasUnsafe method register types , members and extension methods of some type that might be considered most unsafe such as (Console, DirectionInfo, FileInfo, etc.). You can find the complete list here:                                                            |
| RegisterDomainAssemblies           | The RegisterDomainAssemblies method registers all types from all assemblies in loaded in the current domain. This method is only supported in the .NET Framework.                                                                                   |
| RegisterExtensionMethod           | The RegisterExtensionMethod method registers all extension methods from the types or method list provided.                                                                                                                                                                                                             |
| RegisterGlobalConstant             | The RegisterGlobalConstant method registers a global constant value. The constant value is shared across all evaluations but cannot be modified (not writeable).                                                                                                                                                        |
| RegisterGlobalVariable             | The RegisterGlobalVariable method registers a global variable value. This variable value is shared across all evaluations and can be modified.                                                                                                                                                                          |
| RegisterKeyword                    | The RegisterKeyword method registers a keyword for the specified extension methods. For example, if you register the keyword "isin" for the extension method "IsIn", you can now create an expression such as "x isin list".                                                                                            |
| RegisterLocalVariable              | The RegisterLocalVariable method registers a local variable value. This variable value is not shared across all evaluations and can be modified. In other words, the value is only modified for the current evaluation and doesn't impact the other evaluations.                                                        |
| RegisterMember                     | The RegisterMember method registers all instance members from the types or fields list provided. A member is a Property, Field, Method, or Constructor.                                                                                                                                                                 |
| RegisterNamespace                  | The RegisterNamespace method registers all types in the specified assembly under the specified namespaces.                                                                                                                                                                                                              |
| RegisterStaticField                | The RegisterStaticField method registers all static fields from the types or fields list provided.                                                                                                                                                                                                                      |
| RegisterStaticMember               | The RegisterStaticMember method registers all static members from the types or members list provided. A member is a Property, Field or Method.                                                                                                                                                                          |
| RegisterStaticMethod               | The RegisterStaticMethod method registers all static methods from the types or fields list provided.                                                                                                                                                                                                                    |
| RegisterStaticProperty             | The RegisterStaticProperty method registers all static properties from the types or properties list provided.                                                                                                                                                                                                           |
| RegisterType                       | The RegisterType method registers all types provided. The method also register extension methods from types but doesn't registers other static member. For example, if you register the type "Z.MyNamespace.MyClass", you can now create an expression such as "new MyClass()" or any extension methods from this type. |

## Unregister Methods

The `Unregister` methods allows you to remove types, members, and anything else that has been added previously to the library. An excellent way to start fresh is using the `UnregisterAll` method, which will unregister everything.

| Name                                 | Description                                                                                                                                                                       |
| ------------------------------------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| UnregisterAlias                      | The UnregisterAlias method unregisters an alias for a variable, constant, static method or type name.                                                                             |
| UnregisterAll                        | The UnregisterAll method unregister everything (alias, keyword, member, type and more).                                                                                           |
| UnregisterAssembly                   | The UnregisterAssembly method unregister all types from the assemblies provided.                                                                                                  |
| UnregisterAutoAddMissingTypeAssembly | The UnregisterAutoAddMissingTypeAssembly method unregisters assemblies in which the option \`AutoAddMissingTypes\` or \`RetryAndThrowMissingTypes\` is allowed to retrieve types. |
| UnregisterExtensionMethod            | The UnregisterExtensionMethod method unregisters all extension methods from the types or method list provided.                                                                    |
| UnregisterGlobalConstant             | The UnregisterGlobalConstant method unregisters a global constant value.                                                                                                          |
| UnregisterGlobalVariable             | The UnregisterGlobalVariable method unregisters a global variable value.                                                                                                          |
| UnregisterKeyword                    | The UnregisterKeyword method unregisters a keyword for the specified extension methods.                                                                                           |
| UnregisterLocalVariable              | The UnregisterLocalVariable method unregisters a local variable value.                                                                                                            |
| UnregisterMember                     | The UnregisterMember method unregisters all instance members from the types or fields list provided.                                                                              |
| UnregisterNamespace                  | The UnregisterNamespace method unregisters all types in the specified assembly under the specified namespaces.                                                                    |
| UnregisterStaticField                | The UnregisterStaticField method unregisters all static fields from the types or fields list provided.                                                                            |
| UnregisterStaticMember               | The UnregisterStaticMember method unregisters all static fields from the types or fields list provided.                                                                           |
| UnregisterStaticMethod               | The UnregisterStaticMethod method unregisters all static methods from the types or fields list provided.                                                                          |
| UnregisterStaticProperty             | The UnregisterStaticProperty method unregisters all static properties from the types or properties list provided.                                                                 |
| UnregisterType                       | The UnregisterType method unregisters all types provided. The method also unregister extension methods from types                                                                 |

You will find examples for `Unregister` methods inside their corresponding `Register` methods.

## IsRegistered Methods

The `IsRegistered` method allows you to check if a type, member, or anything else has already been registered to our library.

| Name                                   | Description                                                                                                                                                                                      |
| -------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| IsRegisteredAlias                      | The IsRegisteredAlias method return true if the alias or the alias for a specific name provided is registered.                                                                                   |
| IsRegisteredAssembly                   | The IsRegisteredAssembly method return true if all types from the assembly provided is registered.                                                                                               |
| IsRegisteredAutoAddMissingTypeAssembly | The IsRegisteredAutoAddMissingTypeAssembly method return true if the assembly is part in which the option \`AutoAddMissingTypes\` or \`RetryAndThrowMissingTypes\` is allowed to retrieve types. |
| IsRegisteredExtensionMethod            | The IsRegisteredExtensionMethod method return true if all extension methods from the type or extension method provided is registered.                                                            |
| IsRegisteredGlobalConstant             | The IsRegisteredGlobalConstant method return true if the global constant name or the global constant name with the value provided is registered.                                                 |
| IsRegisteredGlobalVariable             | The IsRegisteredGlobalVariable method return true if the global variable name or the global variable name with the value provided is registered.                                                 |
| IsRegisteredKeyword                    | The IsRegisteredKeyword method return true if the keyword provided is registered.                                                                                                                |
| IsRegisteredLocalVariable              | The IsRegisteredLocalVariable method return true if the local variable name or the local variable name with the value provided is registered.                                                    |
| IsRegisteredMember                     | The IsRegisteredMember method return true if all instance members from the type or member provided is registered.                                                                                |
| IsRegisteredNamespace                  | The IsRegisteredNamespace method return true if all types in the assembly under the specified namespaces provided is registered.                                                                 |
| IsRegisteredStaticField                | The IsRegisteredStaticField method return true if all static fields from the type or field provided is registered.                                                                               |
| IsRegisteredStaticMember               | The IsRegisteredStaticMember method return true if all static members from the type or member provided is registered.                                                                            |
| IsRegisteredStaticMethod               | The IsRegisteredStaticMethod method return true if all static methods from the type or method provided is registered.                                                                            |
| IsRegisteredStaticProperty             | The IsRegisteredStaticProperty method return true if all static properties from the type or property provided is registered.                                                                     |
| IsRegisteredType                       | The IsRegisteredType method return true if the type or type name provided is registered.                                                                                                         |

You will find examples for `IsRegistered` methods inside their corresponding `Register` methods.

## RegisterAlias

The RegisterAlias method registers an alias for a variable, constant, method or type name. For example, if you register the alias "mymath" for the class "System.Math", you can now create an expression such as "mymath.Min(1, 2)".

In this example, we will use the alias instead of the real namespace, method, and variable names. In the first example, we will use the `mymath` and `One` alias that will replace the `Math` namespace and `x` variable name. In the second example, we will also use the `MathMin` that will be an alias for the method `Math.Min`.

Afterward, we will use the method `IsRegisteredAlias` to show that the alias was registered, then use the method `UnregisterAlias` to remove it and check again to show that the alias was successfully unregistered.

```csharp
// Global Context: EvalManager.DefaultContext.RegisterAlias("mymath", "Math");
// Global Context: EvalManager.DefaultContext.UnregisterAlias("mymath");
// Global Context: EvalManager.DefaultContext.IsRegisteredAlias("mymath");

var context = new EvalContext();
context.RegisterAlias("mymath", "Math");
context.RegisterAlias("One", "x");
context.RegisterAlias("MathMin", "Min");

// "mymath" is an alias for "Math"
// "One" is an alias for "x" (the parameter)
var r1 = context.Execute("mymath.Min(One, y)", new { x = 1, y = 2 });
Console.WriteLine("1 - Result: " + r1);

// "MathMin" is an alias for "Min" method from "Math.Min"
var r2 = context.Execute("MathMin(One, y)", new { x = 1, y = 2 });
Console.WriteLine("2 - Result: " + r2);

// Check that the alias "mymath" is registered
var r3 = context.IsRegisteredAlias("mymath");
Console.WriteLine("3 - Result: " + r3);

// Unregister the "mymath" alias
context.UnregisterAlias("mymath");

// Check that the alias "mymath" has been succesfully unregistered
var r4 = context.IsRegisteredAlias("mymath");
Console.WriteLine("4 - Result: " + r4);
```

{% include component-try-it.html href='https://dotnetfiddle.net/F0at48' %}

## RegisterAssembly

The RegisterAssembly method register all types from the assemblies provided.

In this example, we will register our assembly to be able to use the `Program.AddDemo` static methods. Afterward, we will use the method `IsRegisteredAssembly` to check that the assembly was indeed registered, the method `UnregisterAssembly` to unregister the assembly, and finally, check again with the method `IsRegisteredAssembly` that the assembly has been successfully unregistered.

```csharp
public class Program
{
	public static void Main()
	{
		// Global Context: EvalManager.DefaultContext.RegisterAssembly(typeof(Program).Assembly);
		
		var context = new EvalContext();
		context.RegisterAssembly(typeof(Program).Assembly);
		
		var r1 = context.Execute<int>("Program.AddDemo(x, y)", new { x = 1, y = 2 });
		Console.WriteLine("1 - Result: " + r1);
		
		// Check if the assembly is registered
		var r2 = context.IsRegisteredAssembly(typeof(Program).Assembly);
		Console.WriteLine("2 - Result: " + r2);
		
		// Unregister the assembly
		context.UnregisterAssembly(typeof(Program).Assembly);
		
		// Check if the assembly has been succesfully unregistered
		var r3 = context.IsRegisteredAssembly(typeof(Program).Assembly);
		Console.WriteLine("3 - Result: " + r3);		
	}
	
	public static int AddDemo(int x, int y)
	{
		return x + y;
	}
}
```

{% include component-try-it.html href='https://dotnetfiddle.net/gz82By' %}

## RegisterAutoAddMissingTypeAssembly

The RegisterAutoAddMissingTypeAssembly method register assemblies in which the option `AutoAddMissingTypes` or `RetryAndThrowMissingTypes` is allowed to retrieve types.

In this example, we will use the same logic as we did with the `RegisterAssembly` method. The major difference between both methods is types when using `RegisterAutoAddMissingTypeAssembly` are only added to the expression if needed, and it requires either the option [AutoAddMissingTypes](/options#autoaddmissingtypes) or [RetryAndThrowMissingTypes](/options#retryandthrowmissingtypes) to works.

```csharp
public class Program
{
	public static void Main()
	{
		// Global Context: EvalManager.DefaultContext.RegisterAutoAddMissingTypeAssembly(typeof(Program).Assembly);
		
		var context = new EvalContext();
		context.RegisterAutoAddMissingTypeAssembly(typeof(Program).Assembly);
		context.AutoAddMissingTypes = true;
		
		var r1 = context.Execute<int>("Program.AddDemo(x, y)", new { x = 1, y = 2 });
		Console.WriteLine("1 - Result: " + r1);
		
		// Check if the assembly is registered
		var r2 = context.IsRegisteredAutoAddMissingTypeAssembly(typeof(Program).Assembly);
		Console.WriteLine("2 - Result: " + r2);
		
		// Unregister the assembly
		context.UnregisterAutoAddMissingTypeAssembly(typeof(Program).Assembly);
		
		// Check if the assembly is registered has been succesfully unregistered
		var r3 = context.IsRegisteredAutoAddMissingTypeAssembly(typeof(Program).Assembly);
		Console.WriteLine("3 - Result: " + r3);		
	}
	
	public static int AddDemo(int x, int y)
	{
		return x + y;
	}
}
```

{% include component-try-it.html href='https://dotnetfiddle.net/IwxHSa' %}

## RegisterDefaultAliasSafe

The RegisterDefaultAliasSafe method register types, members and extensions methods of all safe types consired safe (Array, Dictionary, List, Tuple, etc.). You can find the complete list here:

In this example, we will unregister all types and then register only types included in the `RegisterDefaultAliasSafe` methods. First, we will create a dictionary that is part of the registered types. Then we will try to create a `DataTable` but it will fail as our library exclude it by default as we flag this object unsafe due to method such as `Load` and `WriteXml`.

```csharp
// Global Context: EvalManager.DefaultContext.RegisterDefaultAliasSafe();

var context = new EvalContext();
context.UnregisterAll();
context.RegisterDefaultAliasSafe();

// 1 - You can access the Dictionary object since the type has been registered in the RegisterDefaultAliasSafe
var r1 = context.Execute<int>(@"var dict = new Dictionary<string, object>(); return dict.Count;");

Console.WriteLine("1 - Result: " + r1);

try
{
	var fail = context.Execute<int>("var dt = new DataTable(); return dt.Rows.Count;");
}
catch(Exception ex)
{
	// 2 - However, you cannot access the DataTable because we excluded this type due to methods such as `Load`  and `WriteXml`
	Console.WriteLine("2 - Exception: " + ex.Message);
}
```

{% include component-try-it.html href='https://dotnetfiddle.net/hGumFP' %}

To learn more and see what contains this method, see [Register Default Alias Safe/Unsafe](/register-default-alias-safe-unsafe)

## RegisterDefaultAliasUnsafe

The RegisterDefaultAliasUnsafe method register types , members and extension methods of some type that might be considered most unsafe such as (Console, DirectionInfo, FileInfo, etc.). You can find the complete list here:

In this example, we will unregister all types and then register only types included in the `RegisterDefaultAliasUnsafe` methods. In fact, all types from the `RegisterDefaultAliasUnsafe` are already registered by default, but we still do it for demo purposes. First, we will create a dictionary that is part of the registered types. Then we will create a `DataTable`, but unlike when using the method `RegisterDefaultAliasSafe`, this time, the code will successfully work as the `DataTable` is part of the types registered this time.

```csharp
// Global Context: EvalManager.DefaultContext.RegisterDefaultAliasSafe();

var context = new EvalContext();
context.UnregisterAll();
context.RegisterDefaultAliasUnsafe();

// 1 - You can access the Dictionary object since the type has been registered in the RegisterDefaultAliasSafe
var r1 = context.Execute<int>(@"var dict = new Dictionary<string, object>(); return dict.Count;");

Console.WriteLine("1 - Result: " + r1);

// 1 - You can access the DataTable object with the method `RegisterDefaultAliasUnsafe` unlike the method `RegisterDefaultAliasSafe`
var r2 = context.Execute<int>(@"var dt = new DataTable(); return dt.Rows.Count;");

Console.WriteLine("2 - Result: " + r2);
```

{% include component-try-it.html href='https://dotnetfiddle.net/9tbo3E' %}

To learn more and see what contains this method, see [Register Default Alias Safe/Unsafe](/register-default-alias-safe-unsafe)

## RegisterDomainAssemblies

The RegisterDomainAssemblies method registers all types from all assemblies in loaded in the current domain. This method is only supported in the .NET Framework.

In this example, we will register all assemblies found and use our method `Program.AddDemo`. Keep in mind that using this method registers all types from all assemblies found, which for most scenarios is overkill, so we normally recommend only registering types or assemblies that you really need for the compilation.

```csharp
public class Program
{
	public static void Main()
	{
		// Global Context: EvalManager.DefaultContext.RegisterDomainAssemblies();
		
		var context = new EvalContext();
		context.RegisterDomainAssemblies();
		
		var r1 = context.Execute<int>("Program.AddDemo(x, y)", new { x = 1, y = 2 });
		Console.WriteLine("1 - Result: " + r1);
	}
	
	public static int AddDemo(int x, int y)
	{
		return x + y;
	}
}
```

{% include component-try-it.html href='https://dotnetfiddle.net/4fY2GV' %}

## RegisterExtensionMethod

The RegisterExtensionMethod method registers all extension methods from the types or method list provided.

In this example, we will register an extension method and use it. Then we will check if the extension method was registered with the `IsRegisteredExtensionMethod` method, unregister it with the method `UnregisterExtensionMethod` and verify that the method was correctly unregistered.

```csharp
public class Program
{
	public static void Main()
	{
		// Global Context: EvalManager.DefaultContext.RegisterExtensionMethods(typeof(Extensions));
		
		var context = new EvalContext();
		context.RegisterExtensionMethod(typeof(Extensions));
		
		var r1 = context.Execute<int>("x.AddDemo(y)", new { x = 1, y = 2 });
		Console.WriteLine("1 - Result: " + r1);
		
		// Check if the extension methods is registered
		var r2 = context.IsRegisteredExtensionMethod(typeof(Extensions));
		Console.WriteLine("2 - Result: " + r2);
		
		// Unregister the extension methods
		context.UnregisterExtensionMethod(typeof(Extensions));
		
		// Check if the extension methods has been succesfully unregistered
		var r3 = context.IsRegisteredExtensionMethod(typeof(Extensions));
		Console.WriteLine("3 - Result: " + r3);
	}
}

public static class Extensions
{
	public static int AddDemo(this int x, int y)
	{
		return x + y;
	}
}
```

{% include component-try-it.html href='https://dotnetfiddle.net/w74HZQ' %}

## RegisterGlobalConstant

The RegisterGlobalConstant method registers a global constant value. The constant value is shared across all evaluations but cannot be modified (not writeable).

In this example, we will first register the constant 'x' with the value equal to 1. First, we will execute a simple addition expression that will work. Then, we will try to assign a new value to our constant 'x', which will fail since a constant is not writeable. Finally, we will use the `IsRegisteredGlobalConstant` and `UnregisterGlobalConstant` methods to show the constant was registered and successfully unregistered.

```csharp
// Global Context: EvalManager.DefaultContext.RegisterGlobalConstant("x", 1)

var context = new EvalContext();
context.RegisterGlobalConstant("x", 1);

var r1 = context.Execute<int>("x + y", new { y = 2 });
Console.WriteLine("1 - Result: " + r1);

try
{
	// Try to change the constant value
	var fail = context.Execute("x = 2; return x;");
}
catch(Exception ex)
{
	Console.WriteLine("2 - Exception: " + ex.Message);
}

// Check if the constant "x" is registered
var r3 = context.IsRegisteredGlobalConstant("x");
Console.WriteLine("3 - Result: " + r3);

// Unregister the constant "x"
context.UnregisterGlobalConstant("x");

// Check if the constant "x" has been succesfully unregistered
var r4 = context.IsRegisteredGlobalConstant("x");
Console.WriteLine("4 - Result: " + r4);
```

{% include component-try-it.html href='https://dotnetfiddle.net/fxz6to' %}

> NOTE: A global constant has the same behavior as creating a constant in the code, such as `public const int x = 1;`. You can use the constant value, but you cannot change the value. However, like a `const` you can still add/remove items from a list or change the property values of an entity but not re-assign a new entity itself.

## RegisterGlobalVariable

The RegisterGlobalVariable method registers a global variable value. This variable value is shared across all evaluations and can be modified.

In this example, we will register a global variable. In the first expression, we will increment the value of the global variable and return it. In the second expression, we will return the value of the global variable and notice that it's the same value as the previous one since the variable is shared across all expressions. Lastly, we will check using `IsRegisteredGlobalVariable` and `UnregisterGlobalVariable` methods that the global variable was successfully registered and unregistered.

```csharp
// Global Context: EvalManager.DefaultContext.RegisterGlobalVariable("x", 1);

var context = new EvalContext();
context.RegisterGlobalVariable("x", 1);

var r1 = context.Execute<int>("x++; return x"); // return 2
Console.WriteLine("1 - Result: " + r1);

var r2 = context.Execute<int>("return x"); // return 2
Console.WriteLine("2 - Result: " + r2);

// Check if the global variable "x" is registered
var r3 = context.IsRegisteredGlobalVariable("x"); // return true
Console.WriteLine("3 - Result: " + r3);

// Unregister the global variable "x"
context.UnregisterGlobalVariable("x");

// Check if the global variable "x" has been succesfully unregistered
var r4 = context.IsRegisteredGlobalVariable("x"); // return false
Console.WriteLine("4 - Result: " + r4);
```

{% include component-try-it.html href='https://dotnetfiddle.net/7TjwLN' %}

> NOTE: A global variable has the same behavior as creating a static variable in the code, such as `public static int x = 1;`. The variable is readable and writable, so when changing the value, all codes accessing this variable also now have the modified value.

## RegisterKeyword

The RegisterKeyword method registers a keyword for the specified extension methods. For example, if you register the keyword "isin" for the extension method "IsIn", you can now create an expression such as "x isin list".

In this example, we will first create and register an extension method, then register the keyword "isin" that we can now use to call the `IsIn` extension methods directly. In the first expression, `2 isin list`, we will show an example of how the keyword can be used. Then we will check if the keyword was successfully registered and unregistered with the `IsRegisteredKeyword` and `UnregisterKeyword` methods.

```csharp
public class Program
{
	public static void Main()
	{
		// Global Context: EvalManager.DefaultContext.RegisterKeyword("isin", "IsIn");
		
		var context = new EvalContext();
		context.RegisterExtensionMethod(typeof(CustomExtensions));;
		context.RegisterKeyword("isin", "IsIn");
		
		var list = new List<int>() { 1, 2, 3 };
		
		var r1 = context.Execute<bool>("2 isin list", new { list });
		Console.WriteLine("1 - Result: " + r1);
		
		// Check if the keyword "isin" is registered
		var r2 = context.IsRegisteredKeyword("isin"); // return true
		Console.WriteLine("2 - Result: " + r2);
		
		// Unregister the keyword "isin"
		context.UnregisterKeyword("isin");
		
		// Check if the keyword "isin" has been succesfully unregistered
		var r3 = context.IsRegisteredKeyword("isin"); // return false
		Console.WriteLine("3 - Result: " + r3);
	}
}

public static class CustomExtensions
{
	public static bool IsIn<T>(this T x, List<T> list)
	{
		return list.Contains(x);
	}
}
```

{% include component-try-it.html href='https://dotnetfiddle.net/VvdzH4' %}

## RegisterLocalVariable

The RegisterLocalVariable method registers a local variable value. This variable value is not shared across all evaluations and can be modified. In other words, the value is only modified for the current evaluation and doesn't impact the other evaluations.

In this example, we will register a global variable. In the first expression, we will increment the value of the global variable and return it. In the second expression, we will return the value of the global variable and notice that the value is still `1`. That's because, unlike the global variable, the local variable value scope is only for the current expression. Lastly, we will check using `IsRegisteredLocalVariable` and `UnregisterLocalVariable` methods that the global variable was successfully registered and unregistered.

```csharp
// Global Context: EvalManager.DefaultContext.RegisterLocalVariable("x", 1);

var context = new EvalContext();
context.RegisterLocalVariable("x", 1);

var r1 = context.Execute<int>("x++; return x"); // return 2
Console.WriteLine("1 - Result: " + r1);

var r2 = context.Execute<int>("return x"); // return 1
Console.WriteLine("2 - Result: " + r2);

// Check if the local variable "x" is registered
var r3 = context.IsRegisteredLocalVariable("x"); // return true
Console.WriteLine("3 - Result: " + r3);

// Unregister the local variable "x"
context.UnregisterLocalVariable("x");

// Check if the local variable "x" has been succesfully unregistered
var r4 = context.IsRegisteredLocalVariable("x"); // return false
Console.WriteLine("4 - Result: " + r4);
```

{% include component-try-it.html href='https://dotnetfiddle.net/L9edtq' %}

> NOTE: A local variable has the same behavior as passing a variable in a parameter to a method. Whenever you change the variable's value in the method, the value used to call the method is not modified. However, if you pass an object such as a `List` and `Add` an item, the list used when calling the method is also modified.

## RegisterMember

The RegisterMember method registers all instance members from the types or fields list provided. A member is a Property, Field, Method, or Constructor.

In this example, we will first set our context in safe mode with `context.SafeMode = true;` and unregister all current types with `context.UnregisterAll();`. We need to set `SafeMode = true` as; otherwise, members such as methods will be automatically accessible. In the first expression, we will try to access the `GetCustomerID` method, which will fail since we are in `SafeMode`, and then we will register the member and try again, which this time will success. To finish, we will use our methods `IsRegisteredMember` and `UnregisterMember` to unregister all methods from our `Customer` type and make sure it has been successfully unregistered.

```csharp
public class Program
{
	public static void Main()
	{
		// Global Context: EvalManager.DefaultContext.RegisterMember(typeof(Customer));
		
		var context = new EvalContext();
		context.SafeMode = true;
		context.UnregisterAll();		
		
		var customer = new Customer() { CustomerID = 1 };
		
		try
		{
			var fail = context.Execute<int>("GetCustomerID()", customer);
		}
		catch(Exception ex)
		{
			Console.WriteLine("1 - Exception: " + ex.Message);
		}
		
		context.RegisterMember(typeof(Customer));		
		
		var r2 = context.Execute<int>("GetCustomerID()", customer);
		Console.WriteLine("2 - Result: " + r2);
		
		// Check if all members of the type `Customer` is registered
		var r3 = context.IsRegisteredMember(typeof(Customer)); // return true
		Console.WriteLine("3 - Result: " + r3);
		
		// Unregister all members of the type `Customer`
		context.UnregisterMember(typeof(Customer));
		
		// Check if all members of the type `Customer` has been succesfully unregistered
		var r4 = context.IsRegisteredMember(typeof(Customer)); // return false
		Console.WriteLine("4 - Result: " + r4);
	}
	
	public class Customer
	{
		public int CustomerID { get; set; }
		
		public int GetCustomerID()
		{
			return CustomerID;
		}
	}
}
```

{% include component-try-it.html href='https://dotnetfiddle.net/SQXTNA' %}

## RegisterNamespace

The RegisterNamespace method registers all types in the specified assembly under the specified namespaces.

In this example, we will register all members of the namespace `Z2`. In the first expression, we will try to create a new instance of the `Z1.Customer1`, which will fail since members under the namespace `Z1` has not been registered. In the second expression, we will successfully create a new instance of the `Z2.Customer2` customer, as we initially registered all members under the `Z2` namespace. To complete our example, we will use the methods `IsRegisteredNamespace` and `UnregisterNamespace` to check if all members have been registered and unregister members under this namespace.

```csharp
public class Program
{
	public static void Main()
	{
		// Global Context: EvalManager.DefaultContext.RegisterMember(typeof(Customer));
		
		var context = new EvalContext();
		context.RegisterNamespace(typeof(Program).Assembly, "Z2");	
		
		try
		{
			var fail = context.Execute<int>("new Customer1();");
		}
		catch(Exception ex)
		{
			Console.WriteLine("1 - Exception: " + ex.Message);
		}	
		
		var r2 = context.Execute("new Customer2();");
		Console.WriteLine("2 - Result: " + r2);
		
		// Check if all members of the namespace "Z" is registered
		var r3 = context.IsRegisteredNamespace(typeof(Program).Assembly, "Z2"); // return true
		Console.WriteLine("3 - Result: " + r3);
		
		// Unregister all members of the namespace "Z"
		context.UnregisterNamespace(typeof(Program).Assembly, "Z2");
		
		// Check if all members of the namespace "Z" has been succesfully unregistered
		var r4 = context.IsRegisteredNamespace(typeof(Program).Assembly, "Z2"); // return false
		Console.WriteLine("4 - Result: " + r4);
	}
}

namespace Z1
{
	public class Customer1
	{
		public int CustomerID { get; set; }
	}
}

namespace Z2
{	
	public class Customer2
	{
		public int CustomerID { get; set; }
	}
}
```

{% include component-try-it.html href='https://dotnetfiddle.net/RjxR7Z' %}

## RegisterStaticField

The RegisterStaticField method registers all static fields from the types or fields list provided.

In this example, we will register all static fields of the type `Program` with the method `RegisterStaticField` and then use it in our expressions. Then we will demonstrate how to use the method `IsRegisteredStaticField` and `UnregisterStaticField` to check if all statics fields are currently registered and to unregister them.

```csharp
using System;
using Z.Expressions;

public class Program
{
	public static void Main()
	{
		// Global Context: EvalManager.DefaultContext.RegisterStaticField(typeof(Program));
		
		var context = new EvalContext();
		context.RegisterStaticField(typeof(Program));		
		
		var r1 = context.Execute<int>("MyStaticField + 100"); // return 101
		Console.WriteLine("1 - Result: " + r1);
		
		// Check if all static fields of the type `Program` is registered
		var r2 = context.IsRegisteredStaticField(typeof(Program)); // return true
		Console.WriteLine("2 - Result: " + r2);
		
		// Unregister all static fields of the type `Program`
		context.UnregisterStaticField(typeof(Program));
		
		// Check if all static fields of the type `Program` has been succesfully unregistered
		var r3 = context.IsRegisteredStaticField(typeof(Program)); // return false
		Console.WriteLine("3 - Result: " + r3);
	}
	
	public static int MyStaticField = 1;
}
```

{% include component-try-it.html href='https://dotnetfiddle.net/mleWka' %}

## RegisterStaticMember

The RegisterStaticMember method registers all static members from the types or members list provided. A member is a Property, Field or Method.

In this example, we will register all static fields, properties, and methods of the type `Program` with the method `RegisterStaticMember` and then use it in our expressions. Then we will demonstrate how to use the method `IsRegisteredStaticMember` and `UnregisterStaticMember` to check if all statics members are currently registered and to unregister them.
```csharp
using System;
using Z.Expressions;

public class Program
{
	public static void Main()
	{
		// Global Context: EvalManager.DefaultContext.RegisterStaticMember(typeof(Program));
		
		var context = new EvalContext();
		context.RegisterStaticMember(typeof(Program));		
		
		var r1 = context.Execute<int>("MyStaticField + MyStaticProperty + MyStaticMethod()"); // return 6
		Console.WriteLine("1 - Result: " + r1);
		
		// Check if all static members of the type `Program` is registered
		var r2 = context.IsRegisteredStaticMember(typeof(Program)); // return true
		Console.WriteLine("2 - Result: " + r2);
		
		// Unregister all static members of the type `Program`
		context.UnregisterStaticMember(typeof(Program));
		
		// Check if all static members of the type `Program` has been succesfully unregistered
		var r3 = context.IsRegisteredStaticMember(typeof(Program)); // return false
		Console.WriteLine("3 - Result: " + r3);
	}
	
	public static int MyStaticField = 1;
	public static int MyStaticProperty { get; set; } = 2;
	public static int MyStaticMethod()
	{
		return 3;
	}
}
```

{% include component-try-it.html href='https://dotnetfiddle.net/ia50Ue' %}

## RegisterStaticMethod

The RegisterStaticMethod method registers all static methods from the types or fields list provided.

In this example, we will register all static methods of the type `Program` with the method `RegisterStaticMethod` and then use it in our expressions. Then we will demonstrate how to use the method `IsRegisteredStaticMethod` and `UnregisterStaticMethod` to check if all statics methods are currently registered and to unregister them.

```csharp
using System;
using Z.Expressions;

public class Program
{
	public static void Main()
	{
		// Global Context: EvalManager.DefaultContext.RegisterStaticMethod(typeof(Program));
		
		var context = new EvalContext();
		context.RegisterStaticMethod(typeof(Program));		
		
		var r1 = context.Execute<int>("MyStaticMethod() + 100"); // return 103
		Console.WriteLine("1 - Result: " + r1);
		
		// Check if all static methods of the type `Program` is registered
		var r2 = context.IsRegisteredStaticMethod(typeof(Program)); // return true
		Console.WriteLine("2 - Result: " + r2);
		
		// Unregister all static methods of the type `Program`
		context.UnregisterStaticMethod(typeof(Program));
		
		// Check if all static methods of the type `Program` has been succesfully unregistered
		var r3 = context.IsRegisteredStaticMethod(typeof(Program)); // return false
		Console.WriteLine("3 - Result: " + r3);
	}
	
	public static int MyStaticMethod()
	{
		return 3;
	}
}
```

{% include component-try-it.html href='https://dotnetfiddle.net/caBvAO' %}

## RegisterStaticProperty

The RegisterStaticProperty method registers all static properties from the types or properties list provided.

In this example, we will register all static properties of the type `Program` with the method `RegisterStaticProperty` and then use it in our expressions. Then we will demonstrate how to use the method `IsRegisteredStaticProperty` and `UnregisterStaticProperty` to check if all statics properties are currently registered and to unregister them.

```csharp
using System;
using Z.Expressions;

public class Program
{
	public static void Main()
	{
		// Global Context: EvalManager.DefaultContext.RegisterStaticProperty(typeof(Program));
		
		var context = new EvalContext();
		context.RegisterStaticProperty(typeof(Program));		
		
		var r1 = context.Execute<int>("MyStaticProperty + 100"); // return 102
		Console.WriteLine("1 - Result: " + r1);
		
		// Check if all static properties of the type `Program` is registered
		var r2 = context.IsRegisteredStaticProperty(typeof(Program)); // return true
		Console.WriteLine("2 - Result: " + r2);
		
		// Unregister all static properties of the type `Program`
		context.UnregisterStaticProperty(typeof(Program));
		
		// Check if all static properties of the type `Program` has been succesfully unregistered
		var r3 = context.IsRegisteredStaticProperty(typeof(Program)); // return false
		Console.WriteLine("3 - Result: " + r3);
	}
	
	public static int MyStaticProperty { get; set; } = 2;
}
```

{% include component-try-it.html href='https://dotnetfiddle.net/b5uNyD' %}

## RegisterType

The RegisterType method registers all types provided. The method also register extension methods from types but doesn't registers other static member. For example, if you register the type "Z.MyNamespace.MyClass", you can now create an expression such as "new MyClass()" or any extension methods from this type.

In this example, we will register the type `MyClassHelper` and the type `MyExtensions` to register his extension methods. In the first expression, we will use our class and our extension methods with a simple arithmetic operation. Then we will demonstrate how to use the method `IsRegisteredType` and `UnregisterType` with our `MyClassHelper` to validate if the type is currently registered and to unregister it.

```csharp
using System;
using System.Collections.Generic;
using Z.Expressions;

public class Program
{
	public static void Main()
	{
		// Global Context: EvalManager.DefaultContext.RegisterType(typeof(Helper));
		
		var context = new EvalContext();
		context.RegisterType(typeof(MyClassHelper));
		context.RegisterType(typeof(MyExtensions));
		
		var r1 = context.Execute<int>("new MyClassHelper().MyClassHelperID + 2.AddMe(3)"); // return 6
		Console.WriteLine("1 - Result: " + r1);
		
		// Check if the type `MyClassHelper` is registered
		var r2 = context.IsRegisteredType(typeof(MyClassHelper)); // return true
		Console.WriteLine("2 - Result: " + r2);
		
		// Unregister the type `MyClassHelper`
		context.UnregisterType(typeof(MyClassHelper));
		
		// Check the type `MyClassHelper` has been succesfully unregistered
		var r3 = context.IsRegisteredType(typeof(MyClassHelper)); // return false
		Console.WriteLine("3 - Result: " + r3);
	}
	
	public class MyClassHelper
	{
		public int MyClassHelperID { get; set; } = 1;
	}
}

public static class MyExtensions
{	
	public static int AddMe(this int x, int y)
	{
		return x + y;
	}
}
```

{% include component-try-it.html href='https://dotnetfiddle.net/LCmJGy' %}