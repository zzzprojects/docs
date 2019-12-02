# Query Filter

## Description
The **Query Filter** feature allows you to filter returned entities from a query using a predicate (**where clause**).

```csharp
public class EntityContext : DbContext
{
	public EntityContext() : base(FiddleHelper.GetConnectionStringSqlServer())
	{
		// Add your Global Query Filter here
		this.Configuration.QueryFilter.Filter<ISoftDelete>(customer => !customer.IsDeleted);
	}
	
	public DbSet<Customer> Customers { get; set; }
}

// SELECT * FROM Customers WHERE IsDeleted = 0
var list = context.Customers.ToList();
```
Try it: [NET Core](https://dotnetfiddle.net/g1XRz1) | [NET Framework](https://dotnetfiddle.net/aDsTWW)

The filter is applied in the database and application side:
- **Database side**: Whenever possible, the filter is applied in the SQL query.
- **Application side**: The filter is always applied to the query result.

This feature allows to exclude or include specific entities to handle scenarios such as:
- [Soft Delete](#soft-delete)
- [Multi-Tenancy](#multi-tenancy)
- [Logical Data Partitioning](#logical-data-partitioning)
- [Security Access](#security-access)

### What is supported?
All cases are supported:
- Include
- Lazy Loading
- Many to Many
- Etc.

### Advantage

- Centralize logic in **Query Filter** instead of adding it on every queries
- Reduce the chance of missing a filter when creating a new query
- Improve code readability
- Improve development productivity


## Getting Started

### Global Query Filter
You can create a **Global Query Filter** inside your context constructor. This filter will be used by all your context instances.

```csharp
public class EntityContext : DbContext
{
	public EntityContext() : base(FiddleHelper.GetConnectionStringSqlServer())
	{
		// Add your Global Query Filter here
		this.Configuration.QueryFilter.Filter<ISoftDelete>(customer => !customer.IsDeleted);
	}
	
	public DbSet<Customer> Customers { get; set; }
}

// SELECT * FROM Customers WHERE IsDeleted = 0
var list = context.Customers.ToList();
```
Try it: [NET Core](https://dotnetfiddle.net/cgSy5d) | [NET Framework](https://dotnetfiddle.net/7cKY2x)

### Instance Query Filter
You can create an **Instance Query Filter** after a context instance has been created. This filter will be specific to this context instance. If your context instance already has query filter both filters will be enabled.

```csharp
using (var context = new EntityContext())
{
	// Add your Instance Query Filter here
	context.Configuration.QueryFilter.Filter<ISoftDelete>(customer => !customer.IsDeleted);
	
	// SELECT * FROM Customers WHERE IsActive = 1
	var list = context.Customers.ToList();
	
	FiddleHelper.WriteTable("Customers", list);			
}
```
Try it: [NET Core](https://dotnetfiddle.net/S2tCDX) | [NET Framework](https://dotnetfiddle.net/qjRFbZ)

### Enable/Disable Query Filter
You can enable/disable your **Query Filter** with the `Enable()`, `Disable()`, `EnableFilter(id)`, and `DisableFilder(id)` methods.

```csharp
using (var context = new EntityContext())
{
	var filter = context.Configuration.QueryFilter.Filter<ISoftDelete>(QueryFilterType.SoftDelete.ToString(), customer => !customer.IsDeleted);
	
	// QueryFilter.Disable()
	{
		filter.Disable();

		// SELECT * FROM Customers
		var list = context.Customers.ToList();

		FiddleHelper.WriteTable("Customers", list);
	}

	// QueryFilter.Enable()
	{
		filter.Enable();

		// SELECT * FROM Customers WHERE IsActive = 1
		var list = context.Customers.ToList();

		FiddleHelper.WriteTable("Customers", list);
	}
	
	// QueryFilterManager.DisableFilter(string id)
	{
		// DOC: You can enable/disable your `QueryFilter` with the `Enable()`, `Disable()`, `EnableFilter(id)`, and `DisableFilder(id)` methods.
		context.Configuration.QueryFilter.DisableFilter(QueryFilterType.SoftDelete.ToString());

		// SELECT * FROM Customers
		var list = context.Customers.ToList();

		FiddleHelper.WriteTable("Customers", list);
	}

	// QueryFilterManager.EnableFilter(string id)
	{
		// DOC: You can enable/disable your `QueryFilter` with the `Enable()`, `Disable()`, `EnableFilter(id)`, and `DisableFilder(id)` methods.
		context.Configuration.QueryFilter.EnableFilter(QueryFilterType.SoftDelete.ToString());

		// SELECT * FROM Customers WHERE IsActive = 1
		var list = context.Customers.ToList();

		FiddleHelper.WriteTable("Customers", list);
	}
}
```
Try it: [NET Core](https://dotnetfiddle.net/hxOZQz) | [NET Framework](https://dotnetfiddle.net/tctGi0)

> DANGER: DO NOT disable `Global Query Filter` unless you want to disable the filter for all your context instances.

> HINT: Use an `enum` when using a specified ID `QueryFilterType.FilterName.ToString()` to avoid hardcoding a `string`.

## Real Life Scenarios

### Soft Delete
Your application uses Soft Delete/Logical Delete to delete entities.

The **Query Filter** allows you to exclude all entities that are soft deleted from all your queries.

```csharp
public class EntityContext : DbContext
{
	public EntityContext() : base(FiddleHelper.GetConnectionStringSqlServer())
	{
		// Add your Global Query Filter here
		this.Configuration.QueryFilter.Filter<ISoftDelete>(customer => !customer.IsDeleted);
	}
	
	public DbSet<Customer> Customers { get; set; }
}

// SELECT * FROM Customers WHERE IsDeleted = 0
var list = context.Customers.ToList();
```
Try it: [NET Core](https://dotnetfiddle.net/dVOBXI) | [NET Framework](https://dotnetfiddle.net/b1kwHs)

> HINT: The filter is usually applied to an interface named `ISoftDelete` inherited by all entity types that use Soft Delete. 

### Multi-Tenancy
Your application uses a single instance to serve multiple tenants.

The **Query Filter** allows you to include only data that is related to the specified `TenantID` to all your queries.

```csharp
public static int ApplicationTenantID = 1;

public class EntityContext : DbContext
{
	public EntityContext() : base(FiddleHelper.GetConnectionStringSqlServer())
	{
		// Add your Global Query Filter here
		this.Configuration.QueryFilter.Filter<ITenant>(x => x.TenantID == ApplicationTenantID);
	}
	
	public DbSet<Customer> Customers { get; set; }
}

// SELECT * FROM Customers WHERE TenantID = 1
var list = context.Customers.ToList();
```
Try it: [NET Core](https://dotnetfiddle.net/9WAJRN) | [NET Framework](https://dotnetfiddle.net/WuWGCy)

> HINT: The filter is usually applied to an interface named `ITenant` inherited by all entity types that use multi-tenancy.

### Logical Data Partitioning
Your application stores data in the same table but only a specific range should be available by example for a country.

The **Query Filter** allows you to include only data available for the specified country to all your queries.

```csharp
// TODO
// [Try it](8c3zgI)
```

### Security Access
Your application has security access. The logged user can only view some data depending on his role.

The `Query Filter` allows you to include only data the user has access to all your queries.

```csharp
public static int CurrentRoleId = 1; // admin

public class EntityContext : DbContext
{
	public EntityContext() : base(FiddleHelper.GetConnectionStringSqlServer())
	{
		this.Configuration.QueryFilter.Filter<ISoftDelete>(customer => !customer.IsDeleted || CurrentRoleId == 1);
		this.Configuration.QueryFilter.Filter<Customer>(customer => customer.Type == CustomerType.Web || CurrentRoleId == 1);
	}
	
	public DbSet<Customer> Customers { get; set; }
}
```
Try it: [NET Core](https://dotnetfiddle.net/YU8JLJ) | [NET Framework](https://dotnetfiddle.net/HP9Fbe)

## Documentation

### QueryFilter

###### Properties

| Name | Description | Example |
| :--- | :---------- | :------ |
| `ID` | Gets the `QueryFilter` ID. | [NET Core](https://dotnetfiddle.net/5Y5heH) / [NET Framework](https://dotnetfiddle.net/8z8spq) |
| `EntityType` | Gets the `QueryFilter` entity type on which the filter is applied. | [NET Core](https://dotnetfiddle.net/qDSiY3) / [NET Framework](https://dotnetfiddle.net/DP6Del) |
| `IsEnabled` | Gets if the `QueryFilter` is enabled. Use `Enable()` and `Disable()` method to change the state. Always return false if the `QueryFilter` feature is disabled. | [NET Core](https://dotnetfiddle.net/BvMqCw) / [NET Framework](https://dotnetfiddle.net/28AdvH) |

###### Methods

| Name | Description | Example |
| :--- | :---------- | :------ |
| `Enable()` | Enable the `QueryFilter`. | [NET Core](https://dotnetfiddle.net/6BeUbX) / [NET Framework](https://dotnetfiddle.net/H7cqIU) |
| `Disable()` | Disable the `QueryFilter`. | [NET Core](https://dotnetfiddle.net/AIYH9v) / [NET Framework](https://dotnetfiddle.net/ArFGJh) |

### QueryFilterManager

###### Properties

| Name | Description | Example |
| :--- | :---------- | :------ |
| `IsEnabled` | Gets or sets if the `QueryFilter` feature is enabled. | [NET Core](https://dotnetfiddle.net/jAM0rJ) / [NET Framework](https://dotnetfiddle.net/ykhwxO) |

###### Methods

| Name | Description | Example |
| :--- | :---------- | :------ |
| `Filter<T>(Expression<Func<T, bool>> filter)` | Filter an entity type using a predicate. | [NET Core](https://dotnetfiddle.net/fIcXBz) / [NET Framework](https://dotnetfiddle.net/lqfF8b) |
| `Filter<T>(string id, Expression<Func<T, bool>> filter)` | Filter an entity type using a predicate. The `QueryFilter` will be created with the specified ID. | [NET Core](https://dotnetfiddle.net/z4Ls0g) / [NET Framework](https://dotnetfiddle.net/dBOdw2) |
| `EnableFilter(string id)` | Enable the `QueryFilter` with the specified id.  | [NET Core](https://dotnetfiddle.net/beR6We) / [NET Framework](https://dotnetfiddle.net/q7T7nl) |
| `DisableFilter(string id)` | Disable the `QueryFilter` with the specified id. | [NET Core](https://dotnetfiddle.net/x981TW) / [NET Framework](https://dotnetfiddle.net/Zoric3)  |
| `GetFilter(string id)` | Get the `QueryFilter` with the specified id. | [NET Core](https://dotnetfiddle.net/CkHI8j) / [NET Framework](https://dotnetfiddle.net/2IBfGq) |

## Limitations

### Change Tracker
If an entity is already loaded in the `ChangeTracker`, the filter may not apply due to how the `ChangeTracker` works.

For example: 
1. You load a customer with all his invoices
2. You add a filter to the invoice entity type
3. You load the same customer with all his invoices
4. The customer invoices have not been filtered

T
