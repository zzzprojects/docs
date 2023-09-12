---
Name: v7.100.0 - IncludeGraph
LastMod: 2023-09-08
---

# IncludeGraph Breaking Changes & Improvements

Starting with `v7.100.0`, the `IncludeGraph` for EF Core has been entirely rewritten, bringing about the following enhancements:

1. **Reduce Memory Consumption**: The new `IncludeGraph` can reduce memory usage to around 20% of the **memory** its predecessor required, or even less.
2. **Improve Performance**: The new `IncludeGraph` can be up to 5 times faster, and potentially more so when dealing with a very high number of entities.

Projects that frequently use bulk operations will see additional benefits, as the memory collector now has fewer objects to dispose of, leading to an even greater reduction in memory footprint.

However, it's crucial to be aware that the new `IncludeGraph` comes with some [breaking changes](#breaking-changes), detailed later in this document. For those using `IncludeGraph` in their applications, we strongly advise thorough testing before updating your production environment.

## LegacyIncludeGraph

For those who prefer the original `IncludeGraph` functionality from versions prior to `v7.100.0.0`, or encounter issues with the new version, the previous `IncludeGraph` has been renamed to `LegacyIncludeGraph`:

```csharp
context.BulkInsert(list, options => {
	options.LegacyIncludeGraph = true;
});
```

**NOTE**: Please ensure that you do not enable both `IncludeGraph = true` and `LegacyIncludeGraph = true` within the same bulk method. Opt for one or the other.

## Memory & Performance Improvements

The revamped `IncludeGraph` offers a substantial reduction in memory consumption and significant performance enhancements across various scenarios. To demonstrate, consider a scenario with 2000 invoices and 10,000 invoice items:

- [IncludeGraph Demo](https://dotnetfiddle.net/ImBJ1x)
- [LegacyIncludeGraph Demo](https://dotnetfiddle.net/HG7L9q)

---
Benchmark
[Image comparing both fiddles]
---

Though the improvement might seem marginal initially, the new `IncludeGraph` operates twice as quickly and consumes far less memory. The difference becomes even more noticeable as the number of entities rises.

Due to .NET Fiddle's memory constraints, we cannot showcasing examples with a larger number of entities online. However, based on our tests, here's what you can expect:

**10k invoices, 50k InvoiceItem**:

| | IncludeGraph | LegacyIncludeGraph | SaveChanges |
| -- | :--: | :--: | :--: |
| Memory | 100 MB | 200 MB | 200 MB |
| Performance | 1s | 4s | 4s |

**100k invoices, 500k InvoiceItem**:

| | IncludeGraph | LegacyIncludeGraph | SaveChanges |
| -- | :--: | :--: | :--: |
| Memory | 350 MB | 1600 MB | 1500 MB |
| Performance | 8s | 40s | 35s |

TODO: Why SaveChanges faster!

For cases with hundreds of thousands of entities, the new `IncludeGraph` consumes around 20% of the memory that its predecessor did and can perform bulk operations up to 5 times faster. This memory savings can be even more significant in projects that often use bulk operations, as the garbage collector now has fewer objects to manage.

## Limitations

- Does not yet support [Table Splitting](https://learn.microsoft.com/en-us/ef/core/modeling/table-splitting#table-splitting)
- Does not yet support [Entity Splitting](https://learn.microsoft.com/en-us/ef/core/modeling/table-splitting#entity-splitting)

## Breaking Changes

### Default Value Handling

The **primary breaking change** in the new `IncludeGraph` relates to the handling of default values.

Consider the following example:

```csharp
public static void Main()
{
	List<Customer> customers = new List<Customer>();
	customers.Add(new Customer { Name = "ZZZ Projects", MaximumRequestPerDay = 100 });
	customers.Add(new Customer { Name = "Jonathan Magnan" });
}

public class EntityContext : DbContext
{
	protected override void OnModelCreating(ModelBuilder modelBuilder)
	{
		modelBuilder.Entity<Customer>().Property(x => x.MaximumRequestPerDay).HasDefaultValueSql("50");
		base.OnModelCreating(modelBuilder);
	}
	
	public DbSet<Customer> Customers { get; set; }
}

public class Customer
{
	public int CustomerID { get; set; }
	public string Name { get; set; }
	public int MaximumRequestPerDay { get; set; }
}
```

In this example, we've set a default SQL value (`50`) for the `MaximumRequestPerDay` property of the `Customer` class. For the customer **ZZZ Projects**, we have set the `MaximumRequestPerDay` value to `100`, and for the customer **Jonathan Magnan**, we have no specified any value for the `MaximumRequestPerDay` property.

When the model is aware of a SQL default value, a method like `BulkInsert(customers)` will ignore the `MaximumRequestPerDay` property during insertion, regardless of whether a value has been specified. It will always let the database use the default value.

With the `LegacyIncludeGraph` option, the behavior was different. This option relied on EF Core to generate commands, which already handled this scenario perfectly by treating the two entities as two separate inserts as they have different behavior. Which means, that an insert will be used for entities that have a value specified (**ZZZ Projects** have the value `100`), and an insert is created for entities that have no value that will ignore the `MaximumRequestPerDay` property (***Jonathan Magnan** have no value specified).  However, this came at the cost of greater memory consumption and reduced performance.

The new `IncludeGraph` option now generates the command itself, behaving the same way as when no option is specified. This means it will ignore the `MaximumRequestPerDay` property during insertion, regardless of whether a value has been provided.

To force the use of values from the entity, you can utilize the `ForceValueGeneratedStrategy` option:

```csharp
context.BulkInsert(list, options => {
	options.IncludeGraph = true;
	
	// if you want all types to use the same strategy
	// options.ForceValueGeneratedStrategy = Z.EntityFramework.Extensions.ValueGeneratedStrategyType.OnAdd;
	
	options.IncludeGraphOperationBuilder = builder =>
	{
		if(builder is Z.BulkOperations.BulkOperation<Customer> bulk)
		{
			// if you want specific type to use this strategy
			bulk.ForceValueGeneratedStrategy = Z.EntityFramework.Extensions.ValueGeneratedStrategyType.OnAdd;
		}
	};
});
```

If your insert requires both behaviors, we recommend continuing to use the `LegacyIncludeGraph` option.

For a clearer understanding, examine the following examples and their outputs:
- [DefaultValueSql + Without Options](https://dotnetfiddle.net/4KCxfD)
- [DefaultValueSql + LegacyIncludeGraph](https://dotnetfiddle.net/yZA1RR)
- [DefaultValueSql + IncludeGraph](https://dotnetfiddle.net/yBfHSD)
- [DefaultValueSql + IncludeGraph + ValueGeneratedStrategyType.OnAdd](https://dotnetfiddle.net/F2nu0I)

---
[Image Place Holder]
---

### Handling Duplicate Entities in a List

Consider the following example:

```csharp
public class Customer
{
    public int CustomerID { get; set; }
    public string Name { get; set; }
}

List<Customer> customers = new List<Customer>();

var duplicate = new Customer { Name = "ZZZ Projects" };

// Add it twice
customers.Add(duplicate);
customers.Add(duplicate);
```

In this case, the same customer has been added twice to the `customers` list. Depending on how you use `BulkInsert`, different behaviors will manifest:

- **BulkInsert**: The same customer will be inserted twice.
- **BulkInsert + IncludeGraph**: An exception will be thrown. Duplicate entities are not permitted with the new `IncludeGraph`.
- **BulkInsert + LegacyIncludeGraph**: The customer will only be inserted once. The graph identifies the entity as identical within the list and prevents duplicate insertions.

### Handling Duplicate Key

Consider the following example:

```csharp
public class Customer
{
    public int CustomerID { get; set; }
    public string Name { get; set; }
}

List<Customer> customers = new List<Customer>();

customers.Add(new Customer { CustomerID = 1, Name = "ZZZ Projects" });
customers.Add(new Customer { CustomerID = 1, Name = "Jonathan Magnan" });
```

In this scenario, different customers have been added, but they share the same key in the `customers` list. Depending on your `BulkInsert` configuration, different behaviors will emerge:

- **BulkInsert**: SQL Server will throw the error: 'Violation of PRIMARY KEY constraint 'PK_Customers'. Cannot insert duplicate key in object 'dbo.Customers'. The duplicate key value is (1).'
- **BulkInsert + IncludeGraph**: SQL Server will throw the error: 'Violation of PRIMARY KEY constraint 'PK_Customers'. Cannot insert duplicate key in object 'dbo.Customers'. The duplicate key value is (1).'
- **BulkInsert + LegacyIncludeGraph**: The EF Core tracking will throw the error: 'The instance of entity type 'Customer' cannot be tracked because another instance with the same key value for {'CustomerID'} is already being tracked'