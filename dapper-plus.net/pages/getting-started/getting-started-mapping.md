# Mapping

## Description

The mapping allows you to map your entity type with your database table.

Multiple mapping for the same entity type is possible by either using a different context or a mapper key.

There is 3 different way to use the DapperPlusContext:

- Global Context Mapping
- Inheritance Context Mapping
- Instance Context Mapping

They have all pros and cons; choosing the right one will depend on what you need to do. Generally, the Global Context Mapping is recommended.

## Global Context Mapping

The global context mapping allows you to map your entities globally.

This mapping will be used whenever no specific context mapping is provided.

You can either use methods directly from the `DapperPlusManager` or on the `DapperPlusManager.DefaultContext` (both techniques exist for backward compatibility)

```csharp
DapperPlusManager.Entity<Invoice>().Identity(x => x.ID);
connection.BulkInsert(invoices);

// or

DapperPlusManager.DefaultContext.Entity<Invoice>().Identity(x => x.ID);
connection.BulkInsert(invoices);
```

[Try it](https://dotnetfiddle.net/MljjYW)

## Inheritance Context Mapping

The inheritance mapping allows you to map your entities for the scope of the context instance that inherit from the `DapperPlusContext`

1.	Create a class that inherits from the DapperPlusContext
2.	Add the mapping in the constructor

It can be very useful whenever you want different mapping that depends on parameters passed in your constructors.

```csharp
var context = new DapperPlusContext(connection);
context.Entity<Invoice>().Identity(x => x.ID);
context.BulkInsert(invoices);
```

[Try it](https://dotnetfiddle.net/BbBQ2F)

## Instance Context Mapping

The instance mapping allows you to map your entities for the scope of the `DapperPlusContext` instance.

It can be very useful if you need dynamic mapping that always changes.

```csharp
var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServer());

{
	// Use Bulk Operations from Context
	var context = new DapperPlusContext(connection);
	SetMapping(context);
	context.BulkInsert(invoices);
}

{
	// Use Bulk Operations from Connection
	var context = new DapperPlusContext();
	SetMapping(context);
	connection.BulkInsert(context, invoices);
}

		
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
DapperPlusManager.Entity<Invoice>("customMapperKey").Identity(x => x.ID);
connection.BulkInsert("customMapperKey", invoices);
```