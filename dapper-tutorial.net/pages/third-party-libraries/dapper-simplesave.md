---
PermaID: 1000212
Name: Dapper SimpleSave Library
---

# Dapper.SimpleSave Library

## Overview
Dapper.SimpleSave is a small library package which adds basic CRUD operations to Dapper. Dapper easily retrieve data from the database when a single object is involved, but when you are dealing with complex hierarchies, then dapper is not very helpfull. 

This is where Dapper.SimpleSave comes in and save complex objects to the database in a very simple way.

## Attributes
Dapper.SimpleSave need to decorate your domain classes with attribute, for example; 

- Names of the tables and columns in a database.
- Which property represent primary key columns. 
- The cardinality of relationships between tables etc.

```csharp
[Table("Invoices")]
public class Invoice
{
    [PrimaryKey]
    public int? Id { get; set; }
    public string Code { get; set; }
    public InvoiceKind Kind { get; set; }

    [OneToMany]
    public List<InvoiceItem> Items { get; set; }

    [OneToOne]
    public InvoiceDetail Detail { get; set; }
}
```

## NuGet Installation
Dapper.SimpleSave is only available through NuGet: <a href="https://www.nuget.org/packages/Dapper.SimpleSave/" target="_blank">https://www.nuget.org/packages/Dapper.SimpleSave/</a>

You can easily install this library by running the following command:

```csharp
PM> Install-Package Dapper.SimpleSave 
```

More information can be found at: <a href="https://github.com/Paymentsense/Dapper.SimpleSave/" target="_blank">https://github.com/Paymentsense/Dapper.SimpleSave/</a>

## APIs
Once you installed this library then the following extension methods will automatically add to DbConnection:

- Create
- CreateAll
- Update
- UpdateAll
- Delete
- DeleteAll
- SoftDelete
- SoftDeleteAll

You can use these extension methods easily in your code.

```csharp
connection.Create(invoice);

connection.Update(oldInvoice, newInvoice);

connection.Delete(invoice);

connection.SoftDelete(invoice);
```

You can find the detailed documentation here: <a href="https://github.com/Paymentsense/Dapper.SimpleSave/wiki/" target="_blank">https://github.com/Paymentsense/Dapper.SimpleSave/wiki/</a>

## Limitations
Current limitations:
- Convention based mapping are not supported, and you will need to decorate your domain classes with attributes.
- Currently, only **integer**, **long**, and **GUID** primary keys are supported.
- In all your domain classes, a primary key property should be marked with a [PrimaryKey] attribute.

## Support
Unfortunately, this library is not supported.