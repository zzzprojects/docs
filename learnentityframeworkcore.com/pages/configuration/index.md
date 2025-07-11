---
title: Configuration In Entity Framework Core
description: Configuration enables you to override Entity Framework Core's default behaviour in respect of mapping entities and their properties and relationships to a relational database
canonical: /configuration/
status: Published
lastmod: 2025-07-11
---
# EF Core Configuration

Configuration enables you to override Entity Framework Core's default behaviour ([conventions](/conventions)) in respect of mapping entities and their properties and relationships to a relational database. Configuration can be applied in two ways; through decorating classes and properties with [attributes](/configuration/data-annotation-attributes), or by using the [Fluent API](/configuration/fluent-api).

## Attributes

Attributes are used to add metadata to classes, properties, methods etc in the .NET Framework. They are applied to the target class or property by placing them in square brackets (angle brackets if using VB) just before the class or property:

```csharp
[MyClassAttribute]
public class MyClass
{
    [MyPropertyAttribute]
    public int MyProperty { get; set; }
    
    ...
}
```
You can apply multiple attributes in one of two ways - separately:

```csharp
[MyFirstAttribute]
[MySecondAttribute]
[MyThirdAttribute]
public int MyProperty { get; set; }
...
```
or as a comma-separated list:
```csharp
[MyFirstAttribute, MySecondAttribute, MyThirdAttribute]
public int MyProperty { get; set; }
...
```
The attributes used by EF Core reside in the `System.ComponentModel.DataAnnotations` namespace and are often referred to as Data Annotations. 

#### Pros

- Attributes are applied directly to the domain model, so it is easy to see how the model is configured just by examining the class files.
- Some attributes, such as `Required` and `StringLength` are leveraged by client frameworks such as ASP.NET MVC to provide UI-based validation based on the specified configuration. 

#### Cons
Data Annotation attributes can only provide a subset of configuration options. It may also be necessary to use the Fluent API to provide some configuration which will result in configuration being made in more than one place.

## Fluent API

Configuration is applied to entities or their properties via chained methods, which is the identifying feature of the _Fluent API_ pattern.

#### Pros

- The Fluent API provides greater scope for configuration than attributes.
- Configuration can be located in one place, away from the model classes.

#### Cons

Really, there aren't any.

#### Further Reading
- [Data Annotation Attributes](/configuration/data-annotation-attributes)
- [Fluent API](/configuration/fluent-api)
