---
Title: Essentials of Mapping & Modeling: A Comprehensive Guide 
MetaDescription: Essentials of Mapping & Modeling: A Comprehensive Guide 
LastMod: 2024-09-25
---

# Essentials of Mapping: A Comprehensive Guide

To customize how your entity is saved, our library needs precise instructions on **WHAT YOU WANT**:

- The destination table name
- The key identifier
- The properties that should be saved
- And more.

In this documentation, we will explore the three methods you can use to map your entity in Dapper Plus:

- [Auto Mapping](#auto-mapping)
- [Data Annotations](#data-annotations)
- [Fluent API Mapping](#fluent-api-mapping)

This guide aims to provide you with a clear understanding of each mapping approach to ensure your data is handled exactly as you intend.

#Here’s your section on Auto Mapping polished for clarity and to provide detailed insights:

## Auto Mapping

In the simplest scenarios, you won’t need to do anything special. Our library is designed to intelligently discover how to map your entity automatically.

Consider the following C# example, which requires no explicit mapping:

```csharp
var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServer());

connection.BulkInsert(products);
```

[Online Example](https://dotnetfiddle.net/kgIAE1)

If nothing is explicitly specified, our auto mapping will exhibit the following behaviors:
- **Table Name:** The library will use the entity's type name in singular form by default; if it doesn’t find a match, it will attempt to use the plural form.
- **Key:** It will default to using the key defined in your database, if available.
- **Properties:** It will attempt to match each property name with your database column names. If there’s no direct match, especially in cases where column names contain underscores '_' or dots '.', the library will remove these characters to try and find a match.

This approach explains why many examples in our documentation work seamlessly without requiring specific mappings.

However, if you provide an explicit mapping for a property, the auto-mapping will no longer apply its logic to that property, unless you explicitly call the `AutoMap()` method:

```csharp
DapperPlusManager.Entity<Product>().Map(x => "ZZZ " + x.Name, "Name").AutoMap();

var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServer());

connection.BulkInsert(products);
```

[Online Example](https://dotnetfiddle.net/SUAeRl)

## Data Annotations

Data annotations can be used to provide additional instructions to our Auto Mapping, guiding it on how to interpret your class and properties more effectively.

```csharp
[Table("Custom_Product")]
public class Product
{
    [Key]
    [DatabaseGenerated(DatabaseGeneratedOption.Identity)]
    public int ProductID { get; set; }
    
    [Column("Custom_Name")]
    public string Name { get; set; }
    
    [NotMapped]
    public string Description { get; set; }
}

var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServer());

connection.BulkInsert(products);
```

[Online Example](https://dotnetfiddle.net/QeJb8Y)

Unlike ORMs like [Entity Framework](https://www.learnentityframeworkcore.com/configuration/data-annotation-attributes), our library does not require annotations to be from a specific namespace. They can be from `System.ComponentModel...` or any custom namespace, as long as the attribute names match.

Here is a list of supported Data Annotations Attributes that our library recognizes:

- **Table:** Specifies the database table that a class is mapped to.
- **Column:** Maps a property to a column in the database.
- **Key:** Maps the key to use for your entity
- **DatabaseGenerated(DatabaseGeneratedOption.Identity)**: Map a property to act like an identiy
- **NotMapped:** Indicates that a property should not be mapped to any column in the database.

Additional attributes are supported for features like [CreateTable](/create-table), but that is a topic for another discussion.

These annotations allow for fine-tuned control over how entities are mapped to the database, ensuring that your data integration process is as precise and efficient as possible.

## Fluent API Mapping

Fluent API mapping is often the preferred method for many developers as it enhances code readability and maintainability. This approach allows you to chain methods in a manner similar to how you [chain bulk extension methods](/bulk-extensions-methods#chaining) one after another.

```csharp
DapperPlusManager.Entity<Product>()
	.Table("Product")
	.Key(x => x.ProductID)
	.Map(x => new { x.Name, x.Description })
	.MapValue("ZZZ Projects", "Col1")
	.MapWithOptions(x => x.CreatedDate, options => {
		options.FormulaInsert = "GETDATE()";
	})
	.Output(x => new { x.ProductID });
```

[Online Example](https://dotnetfiddle.net/HsRHIp)

Here is a complete list of all methods supported for Fluent API Mapping:

- **Identity:** Sets the property for which the database generates a value. This value is outputted from the destination table (insert and merge actions). The second boolean parameter allows you to choose whether to automatically propagate the identity value (see [Identity Key Propagation](identity-key-propagation#auto-identity-propagation)).
- **Key:** Sets properties to use as a Primary Key (for update, delete, and merge actions).
- **Map:** Maps properties for input to the destination table.
- **Ignore:** Sets properties to ignore. All other columns will be mapped.
- **MapValue:** Maps a constant value (or variable) to the destination columns.
- **MapWithOptions:** Maps a property with specified options.
- **Output:** Specifies which properties should be outputted from the database.
- **Table:** Sets the destination table or view name (including schema name).
- **AutoMap:** Forces auto-mapping even if you have already started manual mapping.

This method of mapping provides extensive control over how your data is transferred to and from the database, ensuring that all operations are executed precisely as intended.

## Conclusion

Mapping alone addresses numerous scenarios, but not all. To fully harness the potential of customizing your saving operations, you will also need to become familiar with [available options](/options) and [mapping keys](/mapping-key).

Currently, there is no single "best way" or recommended method for mapping, as the optimal approach can vary depending on the specifics of your project. In some cases, you may find yourself combining two or three different methods. For example, you might specify only the destination table name and rely on auto mapping to handle the rest.

This flexibility ensures that you can adapt the mapping process to meet the unique needs of each data handling situation, maximizing efficiency and effectiveness in your database interactions.