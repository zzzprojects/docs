---
Name: Register & Unregister Methods
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

We will not make documentation on the `Unregister` and `IsRegistered` methods as we prefer to focus more on the `Register` methods. We believe once you understand once, you can easily understand the purpose of the 2 others prefix.

TODO:

```csharp
// using Z.Expressions; // Don't forget to include this.
var context = new EvalContext();
context.UnregisterAlias("Math2");
context.UnregisterAll();
context.UnregisterAssembly(Assembly.GetEntryAssembly());
context.UnregisterExtensionMethod(typeof(Enumerable));
context.UnregisterExtensionMethod(whereMethodInfo, selectMethodInfo)
context.UnregisterGlobalConstant("sessionMax");
context.UnregisterGlobalVariables("sessionCount");
context.UnregisterStaticMember(typeof(Math), typeof(Int));
context.UnregisterStaticMember(powMemberInfo, RoundMemberInfo);
context.UnregisterType(typeof (bool), typeof(char);
```


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

We will not make documentation on the `Unregister` and `IsRegistered` methods as we prefer to focus more on the `Register` methods. We believe once you understand once, you can easily understand the purpose of the 2 others prefix.

TODO: 

```csharp
// using Z.Expressions; // Don't forget to include this.
var context = new EvalContext();
context.UnregisterAlias("Math2");
context.UnregisterAll();
context.UnregisterAssembly(Assembly.GetEntryAssembly());
context.UnregisterExtensionMethod(typeof(Enumerable));
context.UnregisterExtensionMethod(whereMethodInfo, selectMethodInfo)
context.UnregisterGlobalConstant("sessionMax");
context.UnregisterGlobalVariables("sessionCount");
context.UnregisterStaticMember(typeof(Math), typeof(Int));
context.UnregisterStaticMember(powMemberInfo, RoundMemberInfo);
context.UnregisterType(typeof (bool), typeof(char);
```

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

```csharp
```

{% include component-try-it.html href='#' %}

## RegisterGlobalVariable

The RegisterGlobalVariable method registers a global variable value. This variable value is shared across all evaluations and can be modified.

```csharp
```

{% include component-try-it.html href='#' %}

## RegisterKeyword

The RegisterKeyword method registers a keyword for the specified extension methods. For example, if you register the keyword "isin" for the extension method "IsIn", you can now create an expression such as "x isin list".

```csharp
```

{% include component-try-it.html href='#' %}

## RegisterLocalVariable

The RegisterLocalVariable method registers a local variable value. This variable value is not shared across all evaluations and can be modified. In other words, the value is only modified for the current evaluation and doesn't impact the other evaluations.

```csharp
```

{% include component-try-it.html href='#' %}

## RegisterMember

The RegisterMember method registers all instance members from the types or fields list provided. A member is a Property, Field, Method, or Constructor.

```csharp
```

{% include component-try-it.html href='#' %}

## RegisterNamespace

The RegisterNamespace method registers all types in the specified assembly under the specified namespaces.

```csharp
```

{% include component-try-it.html href='#' %}

## RegisterStaticField

The RegisterStaticField method registers all static fields from the types or fields list provided.

```csharp
```

{% include component-try-it.html href='#' %}

## RegisterStaticMember

The RegisterStaticMember method registers all static members from the types or members list provided. A member is a Property, Field or Method.

```csharp
```

{% include component-try-it.html href='#' %}

## RegisterStaticMethod

The RegisterStaticMethod method registers all static methods from the types or fields list provided.

```csharp
```

{% include component-try-it.html href='#' %}

## RegisterStaticProperty

The RegisterStaticProperty method registers all static properties from the types or properties list provided.

```csharp
```

{% include component-try-it.html href='#' %}

## RegisterType

The RegisterType method registers all types provided. The method also register extension methods from types but doesn't registers other static member. For example, if you register the type "Z.MyNamespace.MyClass", you can now create an expression such as "new MyClass()" or any extension methods from this type.

```csharp
```

{% include component-try-it.html href='#' %}
