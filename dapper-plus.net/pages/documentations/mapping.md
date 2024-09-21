---
Title: Essentials of Mapping & Modeling: A Comprehensive Guide 
MetaDescription: Essentials of Mapping & Modeling: A Comprehensive Guide 
LastMod: 2024-06-27
---

# Essentials of Mapping: A Comprehensive Guide

To save your entity the way **YOU WANT**, our library need to know exactly **WHAT YOU WANT**. The mapping let you configure the [Dapper Plus Context](#) we just previously saw to specify by example:

- What is the destination table name
- What is your key
- Which properties should be saved
- etc.

```csharp
// TODO
```

In this example, we mapped our `Customer` type:
- To the table **WebCustomer**
- With key **CustomerID**
- And to **Automap** all the rest of properties

The mapping alone will cover a lot of scenario but not all of them. We will learn later about [options](#) and [mapping key](#) that will give you additional way to customize how to save your entity. But since we need to start somewhere, let start to learn about the 3 main way to configure your mapping:

- [Auto Mapping](#)
- [Data Annoations](#)
- [Fluent API Mapping](#)

## Auto Mapping

In the most basic scenario, you don't have anything to do. Our library is smart enough to discover how to map your entity automatically.

Let take the following C# example:

```csharp

```

And the corresponding SQL table:

[Screenshot]

Our auto mapping will have the following behavior if nothing explicit is specified:
- **Table Name:** Will use the type name in singular form, if none exists, will try the plural form
- **Key:** Will use the key from your database if one is found
- **Properties:** Will match every properties name with your database column name. Some additional logic apply if your column name contains underscore '_' or dot '.' to remove those if no match is found.

So it explain why many example we previously saw in our Getting Started was already working without having to specify any mapping.

Some part of the auto mapping only work if nothing is explicitely provided. For example, as soon as you map a property, we will no longer map other properties automatically unless you use the `AutoMap()` method such as:

```csharp
context.Entity<T>().Key(x => x.CustomerID).AutoMap()
```

## Data Annotations

You can provide additional indications to our Auto Mapping by providing data annotations to your class and property.

```csharp

```

Unlike ORM like [Entity Framework](https://www.learnentityframeworkcore.com/configuration/data-annotation-attributes), we don't care from which namespace your attribute is coming. Is can be from `System.Component....` or your own custom attribute as long as they have the same name.

Here is a list of supported Data Annoations Attributes:

- **Table:**
- **Column:**
- **NotMapped:**

More attribute are supported for the [CreateTable](#) feature but this is another topic.

## Fluent API Mapping

Mapping through the Fluent API is often the preferred choice as it make your code more readable and easier to maintain. Exactly like when you [Chain Bulk Extensions Methods](#) one after another. 

```csharp

```

Here is a complete list of all methods supported for the Fluent API Mapping:

- Identity	Sets column(s) which the database generates value. The value is outputted from the destination table (insert and merge action).
- Ignore	Sets column(s) to ignore.
- Key	Sets column(s) to use for Primary Key (update, delete, and merge action).
- Map	Sets column(s) to input to the destination table.
- MapValue	Sets value to map to the destination table.
- MapWithOptions	Sets column(s) to input to the destination table with options.
- Output	Sets column(s) to output from the destination table (insert, update, and merge action).
- Table	Sets the destination table or view name (including schema).
- AutoMap

## Conclusion

In this article, we started to explore how to customize your experience with Dapper Plus through the mapping.

There is currently not a "best way" or recommanded way to do your mapping as sometimes you might use 2 or 3 of those solutions at the same times. By example, you might only want to specify the destination table name and let the auto mapping doing the rest.

Dapper Plus is very rich in features. The mapping we just saw only cover common scenario. Our library support way more such as providing inserting only the rows doesn't already exists, auditing, logging, etc.

To gain access to all the power our library offer, we will learn next about [Options and UseBulkOptions method](#).
