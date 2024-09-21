---
Title: 3 Effective Ways to Use the Dapper Plus Context 
MetaDescription: 3 Effective Ways to Use the Dapper Plus Context 
LastMod: 2024-06-27
---

# 3 Effective Ways to Use the Dapper Plus Context

The `DapperPlusContext` is the class where all the magic happen. Every [Bulk Extensions](#) and [Single Extensions](#) methods are executed under an instance of this class even when used through a connection or transaction. The `



main class of Dapper Plus is the . 

We have seen in the both previous getting articles, [Bulk Extensions](#) methods and our 100% free [Single Extensions](#). We have seen that we can execute them from the connection but also from a `DapperPlusContext` but what exactly is this?

To make it very simple, a `DapperPlusContext` is a class that contains all your mapping and options information. Even more, all [Bulk Extensions](#) methods and our 100% free [Single Extensions](#)

you execute a [BulkInsert](/bulk-insert) on a connection or transaction, it's in fact executed inside a context that we call the global context





A `DapperPlusContext` is a class that:
- Will be aware of your mapping
- Will be aware of your options
- Will 

where are they really executed? Really from the connection?


So far, almost all example were basic and didn

we have seen only basic example that doesn't require any configuration 

In the both previous article, we have see  but one question remain is where exactly they are executed?

All our methods are executed within a Dapper Plus Context that we will introduce in this article.



The `DapperPlusContext` class is the center of our library. The purpose of the context is to use the configured mapping and options that we will see later.



We have a global context (`DapperPlusManager.DefaultContext`) that is used whenever you execute a method from a connection or transaction or you can initialize yourself a DapperPlusContext:

```
// use the DapperPlusManager.DefaultContext
connection.BulkInsert(customers);

// use the DapperPlusManager.DefaultContext
transaction.BulkInsert(customers);

var context = new DapperPlusContext(connection);
context.BulkInsert(customers)
```

There is 3 different way to use the DapperPlusContext:

- Global Context
- Instance Context
- Inheritance Context


They all have pros and cons; choosing the right one will depend on what you need to do.

## Global Context Mapping

The global context mapping is the one that is used whenever you execute an action from the `connection` or `transaction`.

This is in fact, simply a new instance of a DapperPlusContext stored in a static variable in the DapperPlusManager:

```csharp
// TODO
```

You can configure mapping and options through directly through the DapperPlusManager or DefaultContext.

You can either use methods directly from the `DapperPlusManager` or on the `DapperPlusManager.DefaultContext` (both techniques exist for backward compatibility)

```csharp
// TODO
```

The global context mapping allows you to map your entities globally.

This mapping will be used whenever no specific context mapping is provided.

```csharp
DapperPlusManager.Entity<Invoice>().Identity(x => x.InvoiceID, true);

connection.BulkInsert(invoices);
```

[Try it](https://dotnetfiddle.net/MljjYW)

One common troubleshooting of people using the global mapping context is that you should do your mapping only once when your application is initialized.

A lot of developer make the mistake to remap the same entity type inside a method which lead to some concurrency error. We will see next the Instance Context Mapping that solve this issue.

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

Or even if the method receive which properties to insert and a custom key:

```csharp
MergeSomething(List column, List keys)
{
}
```

The instance context mapping is one of the most used mapping but sometime you want to keep some variable on your side so this is now the time to introduce how to inherit from a context mapping.

## Inheritance Context Mapping 

The inheritance mapping allows you to map your entities for the scope of the context instance that inherit from the `DapperPlusContext`.

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
context.BulkInsert(invoices);

// connection.BulkInsert(context, invoices); // also available
```

[Try it](https://dotnetfiddle.net/BbBQ2F)

## Conclusion

In this article, we have seen 3 differents way to uses the dapper plus context.

You should not limit yourself to only the global context mapping as it doesn't covert all scenarios.

In the next 3 articles, we will finally discover more about how to use the dapper plus context with (since it was an introduction / prerequiste):
- [Mapping Key](#)
- [Mapping & Modeling](#)
- [OPtions](#)
