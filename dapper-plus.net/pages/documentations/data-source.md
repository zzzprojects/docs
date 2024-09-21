---
Title: Exploring Supported Data Sources in Dapper Plus: What You Need to Know 
MetaDescription: Exploring Supported Data Sources in Dapper Plus: What You Need to Know 
LastMod: 2024-06-27
---

# Exploring Supported Data Sources in Dapper Plus: What You Need to Know 

To really understand this article, make sure to have read our previous one about the [Mapping Key](#) as we will use it for multiples different kind of datasource.

Whenever you need to save data, it is usually done from three different sources:

Strongly Typed Entity
Anonymous Type / Expando Object / Dynamic Type
DataTable and DataRow

It can also come from other datasource such as `XML` and `JSON`, `CSV` but most of the time, it easiers to convert it in datasource we previously described.

## Strongly Typed Entity

Strongly typed entity is what most people usually know. A class with property and field

```csharp
public class Customer
```

You can map the class by using directly the entity type. We usually recommand not setting any mapping key for the generate use of the entity type and a mapping key whenever you need to use options

```csharp
```

## Anonymous Type / Expando Object / Dynamic Object

All those data source are supported but a mapping key is required??? maybe or maybe not

Anonymous Type example

```csharp
```

Expando Object example

```csharp
```

Dynamic Object example:

```csharp
```

## DataTable and DataRows

Using DataRow from a DataTable is also very common especially if your data is comming from a CSV.

To be able to use Dapper Plus with this data source, a mapping key is required to ensure that our library will know what it do. You will have also to use the column name of your data table when performing your mapping as it's not strongly typed.

```csharp

```

## Conclusion

We now learned that most popular data source are supported but sometimes a [mapping key](#) is required as our library cannot assume...

In the next article, we will start to learn about the [identity and key and how to progagate the value].


