---
Title: Exploring Supported Data Sources in Dapper Plus: What You Need to Know 
MetaDescription: Learn how to use Dapper Plus with strongly typed entities, Expando objects, and DataTables.
LastMod: 2025-08-19
---

# Dapper Data Source /n Exploring Supported Data Sources in Dapper Plus: What You Need to Know

To fully benefit from this documentation, ensure you are familiar with the [Mapping Key](/mapping-key) concept, as it plays a crucial role when working with various data sources.

When saving data, it typically originates from one of three primary sources:

- **Strongly Typed Entity:** These are predefined classes with specific properties that correspond to your data model.
- **Expando Object / Dynamic Type:** These types allow for flexible data structures that don't require predefined classes. They are useful for scenarios where the data structure can vary.
- **DataTable and DataRow:** These are used primarily in scenarios where data needs to be handled in a tabular format, often used with legacy systems or when interacting with certain kinds of database operations.

While it's possible to work with other data sources such as XML, JSON, and CSV, it is generally easier to convert these into one of the previously described formats before processing. This approach simplifies the integration and manipulation of data within Dapper Plus.

## Strongly Typed Entity

A strongly typed entity is a common structure in programming, typically a class with properties and fields that represent the data model. This format is straightforward and widely understood, making it a primary choice for many applications.

Here's an example of a strongly typed entity defined for a product:

```csharp
// @nuget: Z.Dapper.Plus
using Z.Dapper.Plus;

[Table("Product")]
public class Product
{
    [Key]
    [DatabaseGenerated(DatabaseGeneratedOption.Identity)]
    public int ProductID { get; set; }
    public string Name { get; set; }
    public string Description { get; set; }
}

var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServer());

connection.BulkInsert(products);
```

[Online Example](https://dotnetfiddle.net/8lPwIb)

This example shows how a class is mapped to a database table and how you can easily perform operations like [bulk insertions](/bulk-insert) using Dapper Plus.

## DapperRow

When using Dapper to query data without specifying a strongly typed entity, as shown previously, a list of `DapperRow` is returned instead. This type can be used like a dynamic object.

Our library can retrieve the property names from the result, but it does not know the associated table name—so you’ll need to specify it manually.

If you want to map through a [DapperPlusContext](https://dapper-plus.net/dapper-plus-context), you’ll need to map using the `object` type, since `DapperRow` is a private class:

```csharp
// @nuget: Z.Dapper.Plus
using Z.Dapper.Plus;

DapperPlusManager.Entity<object>("DapperRow_LegacyProduct")
	.Table("LegacyProduct");
	
// INSERT all products into the "LegacyProduct" table
connection.BulkInsert("DapperRow_LegacyProduct", dapperRows);
```

Alternatively, you can use the [UseBulkOptions](https://dapper-plus.net/options) method to specify the destination table name and any other options:

```csharp
// @nuget: Z.Dapper.Plus
using Z.Dapper.Plus;

connection.UseBulkOptions(options => {
	options.DestinationTableName = "LegacyProduct";
}).BulkInsert(dapperRows);
```

In the following example, we queried data from the `Product` table and inserted all rows into the `ProductLegacy` table:

[Online Example](https://dotnetfiddle.net/D7TVx5)

## Expando Object / Dynamic Object

Our library supports Expando Object / Dynamic Object, which can be particularly useful for scenarios where the data structure is not fixed. However, this typically requires the use of a [mapping key](/mapping-key) or an [Instance Context](/dapper-plus-context#instance-context) to correctly map different expando objects.

In the following example, we create a list of expando objects representing some product and insert them using the `Expando_Product` mapping key:

```csharp
// @nuget: Z.Dapper.Plus
using Z.Dapper.Plus;

DapperPlusManager.Entity<ExpandoObject>("Expando_Product")
	.Table("Product");

var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServer());

connection.BulkInsert("Expando_Product", products);
```

[Online Example](https://dotnetfiddle.net/DmdPUX)

This approach allows for great flexibility in data handling, adapting easily to varying data schemas without needing predefined classes.

## DataTable

Using DataRows from a DataTable is especially common when your data is coming from a CSV file. This method is also favored in scenarios involving legacy systems or when data needs to be manipulated programmatically before being persisted.

To effectively use Dapper Plus with DataTable, a mapping key is essential. This key ensures that our library understands how to map your DataTable to the database schema. Additionally, you must use the column names from your DataTable in your mappings because DataTable are not strongly typed:

```csharp
// @nuget: Z.Dapper.Plus
using Z.Dapper.Plus;

DapperPlusManager.Entity<DataTable>("DataTable_Product")
	.Table("Product")
	.Map("Name")
	.Map("Description");

var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServer());

// DataTable/DataRow
connection.BulkInsert("DataTable_Product", dtProduct);
```

[Online Example](https://dotnetfiddle.net/qu57gL)

This example demonstrates how to execute a [bulk insert](/bulk-insert) operations with DataTables.

## Conclusion

In this documentation, we've learned that Dapper Plus extends beyond merely handling strongly typed lists. It also effectively works with various other data sources such as `ExpandoObject` and `DataTable`. A significant distinction with these non-strongly typed data sources is the requirement to explicitly specify column names in the [Mapping](/mapping) process. Additionally, the use of a [Mapping Key](/mapping-key) is generally necessary to facilitate mapping these types in multiple different ways.

This flexibility allows Dapper Plus to accommodate a wide range of data handling scenarios that you might encounter.