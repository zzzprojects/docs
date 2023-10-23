---
PermaID: 1000186
Title: Dapper Contrib - Learn What Is This Library and How to Use It
MetaDescription: Unlock the power of Dapper Contrib by understanding the basics and more advanced topics of this third-party library. Learn what Dapper Contrib is, how it works, and all the documentation you need to get started.
LastMod: 2023-10-21
---

# Dapper Contrib: Discover What Is This Library and How to Use It

## What's Dapper Contrib?

Dapper Contrib is a 3rd party library that extends the Dapper functionality by providing convenient methods for performing CRUD operations and mapping database results to strongly typed objects.

 - The key feature of Dapper Contrib is its ability to automatically map an object to a table in your database. 
 - It means that you don't have to write any SQL code to CRUD your data. 
 - All you need is your model/entity and Dapper Contrib will handle the rest.
 - It adds basic CRUD operations (Create, Retrieve, Update, Delete) for your models/entities.
 
## Is Dapper Contrib free?

Yes, Dapper Contrib is free and open source.

It is released under the Appache 2.0 license: https://github.com/DapperLib/Dapper.Contrib/blob/main/License.txt

## CRUD Operations

CRUD operations are the basic operations that are performed on a database. CRUD stands for Create, Read, Update and Delete. These operations are the four basic functions that are required to manage data in a database.

 - Create: This operation is used to insert new records into a database table.
 - Read: This operation is used to retrieve data from a database table.
 - Update: This operation is used to update existing records in a database table.
 - Delete: This operation is used to delete records from a database table.

Dapper Contrib is simple yet very powerful and makes working with most databases. It extends the `IDbConnection` interface with additional CRUD methods.

## Installation

Using Dapper Contrib is extremely easy and you can install Dapper Contrib through NuGet: <a href="https://www.nuget.org/packages/Dapper.Contrib/" target="_blank">https://www.nuget.org/packages/Dapper.Contrib/</a> 

To install the Dapper Contrib NuGet Package, run the following command in the Package Manager Console:

```csharp
PM> Install-Package Dapper.Contrib
```

## Methods

Dapper Contrib extends your `IDbConnection` interface with additional CRUD methods:

- [Get](/get)
- [GetAll](/getall)
- [Insert](/insert)
- [Update](/update)
- [Delete](/delete)
- [DeleteAll](/deleteall)

The following code snippet shows how to use the extension methods provided by Dapper Contrib.

```csharp
var invoice = connection.Get<InvoiceContrib>(1);
var invoices = connection.GetAll<InvoiceContrib>().ToList();
var identity = connection.Insert(new InvoiceContrib {Kind = InvoiceKind.WebInvoice, Code = "Insert_Single_1"});
var isSuccess = connection.Update(new InvoiceContrib {InvoiceID = 1, Code = "Update_Single_1"});
var isSuccess = connection.Delete(new InvoiceContrib {InvoiceID = 1});
var isSuccess = connection.DeleteAll<InvoiceContrib>();
```

## Data Annotations

Data annotation is a method used in the object-relational mapping (ORM) to define how data should be mapped to objects. 

 - It is a way of setting up rules and constraints on how data can be stored and accessed. 
 - Data annotation can be used to validate data, specify relationships between objects, and define how data should be stored in the database. 
 - Data annotation can also be used to improve performance by specifying which data should be loaded into memory and which data should be left on the disk.

Dapper Contrib allows mapping using Data Annotations:

- [Key](data-annotation-key)
- [ExplicitKey](data-annotation-explicitkey)
- [Table](data-annotation-table)
- [Write](data-annotation-write)
- [Computed](data-annotation-computed)

The following code snippet shows how to define mapping using Data Annotations provided by Dapper Contrib.

```csharp
[Table("Invoice")]
public class InvoiceContrib
{
	[Key]
	public int InvoiceID { get; set; }

	public string Code { get; set; }
	public InvoiceKind Kind { get; set; }

	[Write(false)]
	[Computed]
	public string FakeProperty { get; set; }
}

[Table("InvoiceDetail")]
public class InvoiceDetailContrib
{
	[ExplicitKey]
	public int InvoiceID { get; set; }

	public string Detail { get; set; }
}
```

## Limitations

There is no support for composite key mapping.

**Dapper.Contrib** has an `Update` extension method, and `SQLiteConnection` also exposes an `Update` event that clashes with each other. But you can easily resolve the clash by using any of the following ways:

- Pass a type parameter to the Update method

```csharp
var isSuccess = connection.Update<InvoiceContrib>(new InvoiceContrib {InvoiceID = 1, Code = "Update_Single_1"});
```

- The other way is to call the Update method explicitly using SqlMapperExtensions.

```csharp
var isSuccess = SqlMapperExtensions.Update(connection, new InvoiceContrib {InvoiceID = 1, Code = "Update_Single_2"});
```

## Related Articles

- [Get](/get)
- [GetAll](/getall)
- [Insert](/insert)
- [Update](/update)
- [Delete](/delete)
- [DeleteAll](/deleteall)