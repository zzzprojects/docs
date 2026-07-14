---
Name: Include Graph
LastMod: 2026-07-14
---

# Include Graph Options in EF Extensions

The `BulkOperation.IncludeGraph` option allows you to `INSERT/UPDATE/MERGE` entities by including the child entities graph. 

In the following example, the `IncludeGraph` is enabled and the list of `Invoice` is added to the database using `BulkInsert`.

```csharp
List<Invoice> list = new List<Invoice>()
{
    new Invoice()
    {
        Description = "Invoice_A",
        InvoiceItems = new List<InvoiceItem>()
        {
            new InvoiceItem() { Description = "Invoice_A_InvoiceItem_A" } ,
            new InvoiceItem() { Description = "Invoice_A_InvoiceItem_B" }
        }
    },
    new Invoice()
    {
        Description = "Invoice_B",
        InvoiceItems = new List<InvoiceItem>()
        {
            new InvoiceItem() { Description = "Invoice_B_InvoiceItem_A" } ,
            new InvoiceItem() { Description = "Invoice_B_InvoiceItem_B" }
        }
    }
};

using (var context = new EntityContext())
{
    context.BulkInsert(list, options => options.IncludeGraph = true);
}

```

[Try it in EF Core](https://dotnetfiddle.net/967y9H) | [Try it in EF6](https://dotnetfiddle.net/spN4T5)

 - It will insert a list of invoices including all the invoices items for each invoice.

## IncludeGraphOperationBuilder
The `IncludeGraphOperationBuilder` lets you customize the bulk operations by entity type.

While some options such as `BatchSize` are copied through the graph, all operations related to columns need to be specified in the `IncludeGraphOperationBuilder` since it depends from a type to another.

The following example uses `Name` property as a key for both `User` and `Role` entities using `IncludeGraphOperationBuilder` to perform `BulkMerge`.

```csharp
ctx.BulkMerge(users, options =>
{
	options.IncludeGraph = true;
	options.IncludeGraphOperationBuilder = operation =>
	{
		if (operation is BulkOperation<User>)
		{
			var bulk = (BulkOperation<User>) operation;
			bulk.ColumnPrimaryKeyExpression = x => x.Name;
		}
		else if (operation is BulkOperation<Role>)
		{
			var bulk = (BulkOperation<Role>) operation;
			bulk.ColumnPrimaryKeyExpression = x => x.Name;
		}
	};
});
```

[Try it in EF Core](https://dotnetfiddle.net/qcjjP0) | [Try it in EF6](https://dotnetfiddle.net/0uW3tw)

### ReadOnly

You can specify that some entities should not be inserted/updated by marking them as ReadOnly.


```csharp
ctx.BulkMerge(users, options =>
{
	options.IncludeGraph = true;
	options.IncludeGraphOperationBuilder = operation =>
	{
		if (operation is BulkOperation<User>)
		{
			var bulk = (BulkOperation<User>) operation;
			bulk.IsReadOnly = true;
		}
	};
});
```

[Try it in EF Core](https://dotnetfiddle.net/LQnvGP) | [Try it in EF6](https://dotnetfiddle.net/UgwDDk)

### LoadLazyNavigations (EF Core)

Starting with **v10.5.7**, you can use the `LoadLazyNavigations` option to control whether `IncludeGraph` should automatically load lazy navigations during graph traversal.

By default, `IncludeGraph` only traverses navigations that are already loaded.

If your project uses EF Core lazy loading, unloaded lazy navigations are skipped during the graph traversal.

You can set `LoadLazyNavigations` to `true` to automatically load those navigations before traversing them.

```csharp
ctx.BulkMerge(users, options =>
{
    options.IncludeGraph = true;
    options.LoadLazyNavigations = true;
});
```

This option is useful when you want `IncludeGraph` to process the complete entity graph without manually loading every navigation first.

> **Note:** This option is only available in EF Core 3.0 or later and only affects lazy navigations. Regular navigations that are not configured for lazy loading are still skipped if they are not loaded.

### Invalid 

{% include section-faq-begin.html %}
## FAQ

### Why do I receive an error that asks me to specify a context factory?
To retrieve the current entities graph, our library requires a working context to use some Entity Framework features without impacting the current context.
The context factory is optional if your context has a default constructor.

Read more: [Context Factory](context-factory)

### Why do I received an error that asks me to use unsafe mode?
The unsafe mode is required when some proxy entities are found in the graph.
To retrieve all information, we need to detach temporary proxy type from the current context before re-attaching them.
There is currently no known issue about this technique, but we prefer to force people to understand that some unsafe code is currently done under the hood by our library.

{% include section-faq-end.html %}
