# Mapping

## Description

The mapping allows you to map your entity type with your database table.

Multiple mapping for the same entity type is possible by either using a different context or a mapper key.

There is 3 different way to use the DapperPlusContext:

- Global Context Mapping
- Inheritance Context Mapping
- Instance Context Mapping

They have all pros and cons; choosing the right one will depend on what you need to do. Generally, the Global Context Mapping is is recommended.

## Global Context Mapping

The global context mapping allows you to map your entities globally.

This mapping will be used whenever no specific context mapping is provided.

You can either use methods directly from the `DapperPlusManager` or on the `DapperPlusManager.DefaultContext` (both techniques exist for backward compatibility)

```csharp
DapperPlusManager.Entity<Invoice>().Identity(x => x.InvoiceID, true);

connection.BulkInsert(invoices);
```

[Try it](https://dotnetfiddle.net/MljjYW)

## Inheritance Context Mapping

The inheritance mapping allows you to map your entities for the scope of the context instance that inherit from the `DapperPlusContext`

1.	Create a class that inherits from the DapperPlusContext
2.	Add the mapping in the constructor

It can be very useful whenever you want different mapping that depends on parameters passed in your constructors.

```csharp
public class InvoiceContext : DapperPlusContext
{
	public InvoiceContext() : base(new SqlConnection(FiddleHelper.GetConnectionStringSqlServer()))
	{
		this.Entity<Invoice>().Identity(x => x.InvoiceID, true);
		this.Entity<InvoiceItem>().Identity(x => x.InvoiceItemID, true);
	}
}

var context = new InvoiceContext();
context.BulkInsert(context, invoices);

// connection.BulkInsert(context, invoices); // also available
```

[Try it](https://dotnetfiddle.net/BbBQ2F)

## Instance Context Mapping

The instance mapping allows you to map your entities for the scope of the `DapperPlusContext` instance.

It can be very useful if you need dynamic mapping that always changes.

```csharp
var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServer());

var context = new DapperPlusContext(connection);
SetMapping(context);
context.BulkInsert(invoices);
		
public static void SetMapping(DapperPlusContext context)
{
	context.Entity<Invoice>().Identity(x => x.InvoiceID);
}
```

[Try it](https://dotnetfiddle.net/WHSCTW)

## Mapper Key

Using a mapper key allow you to map multiple time the same entity type but with a different mapping.

When you call a bulk operations method, you need to pass this mapping key.

```csharp
DapperPlusManager.Entity<Invoice>("CustomKey").Table("Invoice2").Identity(x => x.InvoiceID, true);

connection.BulkInsert("CustomKey", invoices);
```

[Try it](https://dotnetfiddle.net/TmiG4r)