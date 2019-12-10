# Audit

## Description
The **EF Audit** feature allows you to create an audit trail of all changes that occured when saving in Entity Framework. 

The audit trail can be automatically saved in a database or log file.

```csharp
public class EntityContext : DbContext
{
	public EntityContext() : base(FiddleHelper.GetConnectionStringSqlServer())
	{
		// Add your configuration here
		this.Configuration.Audit.AutoSaveSet = this.XmlAuditEntries;
		this.Configuration.Audit.IsEnabled = true;
	}
	
	public DbSet<Customer> Customers { get; set; }
	public DbSet<XmlAuditEntry> XmlAuditEntries { get; set; }
}

public static void Main()
{
	using (var context = new EntityContext())
	{
		context.Customers.Add(new Customer() { Name = "Customer_A", Description = "Description" });
		
		// Save changes and Audit trail in database
		context.SaveChanges();
		
		// Display Audit trail
		var auditEntries = context.XmlAuditEntries.ToAuditEntries();
		FiddleHelper.WriteTable("1 - Audit Entries", auditEntries);
		FiddleHelper.WriteTable("2 - Audit Properties", auditEntries.SelectMany(x => x.Properties));
	}
}
```
Try it: [NET Core](https://dotnetfiddle.net/dxUhEK) | [NET Framework](https://dotnetfiddle.net/1kVazO) 

This feature allows you to handle various scenario such as:
- [Saving audit trail in a database](#saving-audit-trail-in-a-database)
- [Saving audit trail in a log file](#saving-audit-trail-in-a-log-file)
- [Saving audit trail in a different database](#saving-audit-trail-in-a-different-database)
- [Displaying audit trail history](#displaying-audit-trail-history)

### What is supported?
- `SaveChanges()`
- `BatchSaveChanges()`
- `BulkSaveChanges()`
- All types of modifications:
   - `EntityAdded`
   - `EntityModified`
   - `EntityDeleted`
   - `EntitySoftDeleted`
   - `RelationshipAdded`
   - `RelationshipDeleted`

### Advantage
- Track what, who, and when a value is changed
- Keep the audit trail of all changes
- Display the audit trail of all changes

### Community vs Enterprise
The `Audit` feature is free to use in the **Community** version.

The **Enterprise** version offer performance enhancement by automatically saving audit entries using the `BulkInsert`.

## Getting Started

### Enable Auditing
By default, not to impact performance, the **Audit** feature is disabled. You can activate it by enabling the following configuration `context.Configuration.Audit.IsEnabled = true`.

#### Always enabled
To have the **Audit** feature always enabled, you activate it in your context constructor.

```csharp
public class EntityContext : DbContext
{
	public EntityContext() : base(FiddleHelper.GetConnectionStringSqlServer())
	{
		// Add your configuration here
		this.Configuration.Audit.AutoSaveSet = this.XmlAuditEntries;
		this.Configuration.Audit.IsEnabled = true;
	}
	
	public DbSet<Customer> Customers { get; set; }
	public DbSet<XmlAuditEntry> XmlAuditEntries { get; set; }
}

public static void Main()
{
	using (var context = new EntityContext())
	{
		context.Customers.Add(new Customer() { Name = "Customer_A", Description = "Description" });
		
		// Save changes and Audit trail in database
		context.SaveChanges();
		
		// Display Audit trail
		var auditEntries = context.XmlAuditEntries.ToAuditEntries();
		FiddleHelper.WriteTable("1 - Audit Entries", auditEntries);
		FiddleHelper.WriteTable("2 - Audit Properties", auditEntries.SelectMany(x => x.Properties));
	}
}
```
Try it: [NET Core](https://dotnetfiddle.net/5Sd9eQ) | [NET Framework](https://dotnetfiddle.net/gmlMIz)

#### On Demand enabled
To have the **Audit** feature on demand enabled, you activate it after the context is created.

```csharp
public class EntityContext : DbContext
{
	public EntityContext() : base(FiddleHelper.GetConnectionStringSqlServer())
	{
		// Add your configuration here
		this.Configuration.Audit.AutoSaveSet = this.XmlAuditEntries;
	}
	
	public DbSet<Customer> Customers { get; set; }
	public DbSet<XmlAuditEntry> XmlAuditEntries { get; set; }
}

public static void Main()
{
	using (var context = new EntityContext())
	{
		// You can activate the Audit feature on demand by enabling it after the context is created.
		context.Configuration.Audit.IsEnabled = true;
		
		context.Customers.Add(new Customer() { Name = "Customer_A", Description = "Description" });
		
		// Save changes and Audit trail in database
		context.SaveChanges();
		
		// Display Audit trail
		var auditEntries = context.XmlAuditEntries.ToAuditEntries();
		FiddleHelper.WriteTable("1 - Audit Entries", auditEntries);
		FiddleHelper.WriteTable("2 - Audit Properties", auditEntries.SelectMany(x => x.Properties));
	}
}
```
Try it: [NET Core](https://dotnetfiddle.net/ItRyBp) | [NET Framework](https://dotnetfiddle.net/ewsolr)

### Last Audit
The latest audit can be accessed with the `LastAudit` property.

The `LastAudit` property gives you additional information that are not saved such as:
- Entity
- Entry
- OldValueRaw
- NewValueRaw

```csharp
context.Customers.Add(new Customer() { Name = "Customer_A", Description = "Description" });
context.Customers.Add(new Customer() { Name = "Customer_B", Description = "Description" });
context.SaveChanges();

var lastAudit = context.Configuration.Audit.LastAudit;
FiddleHelper.WriteTable("1 - LastAudit - Entries", lastAudit.Entries);
FiddleHelper.WriteTable("2 - LastAudit - Properties", lastAudit.Entries.SelectMany(x => x.Properties));
```
Try it: [NET Core](https://dotnetfiddle.net/riY7U3) | [NET Framework](https://dotnetfiddle.net/qoNEMi)

### AutoSave Set
To automatically save the audit trail in your database, you need to specify the `DbSet<>` in which audit entries will be added then saved.

One of those following set must be added to your context:

| Name | Description | Example |
| :--- | :---------- | :------ |
| `AuditEntry` | The `AuditEntry` class allows you to save one row per property (Only recommended for `Enterprise` version). The `DbSet<AuditEntry>` and `DbSet<AuditEntryProperty>` must be added to your context. | [NET Core](https://dotnetfiddle.net/wHXprT) / [NET Framework](https://dotnetfiddle.net/cg2SVF) |
| `XmlAuditEntry` | The `XmlAuditEntry` class allows you to save your properties in an XML format. The `DbSet<XmlAuditEntry>` must be added to your context. | [NET Core](https://dotnetfiddle.net/9k5CGY) / [NET Framework](https://dotnetfiddle.net/2HLtF4) |

### AutoSave Action
To automatically save the audit trail in another context or a log file, you need to specify an `AutoSaveAction`. This action will be executed after all saves are completed.

```csharp
public EntityContext() : base(FiddleHelper.GetConnectionStringSqlServer())
{
	var audit = this.Configuration.Audit;			
	audit.AutoSaveAction = (context, auditing) => {
		foreach(var entry in auditing.EntriesXml)
		{
			Log.AppendLine("EntitySetName: " + entry.EntitySetName);
			Log.AppendLine("EntityTypeName: " + entry.EntityTypeName);
			Log.AppendLine("State: " + entry.State);
			Log.AppendLine("StateName: " + entry.StateName);
			Log.AppendLine("CreatedBy: " + entry.CreatedBy);
			Log.AppendLine("CreatedDate: " + entry.CreatedDate);
			Log.AppendLine("XmlProperties: " + entry.XmlProperties);
			Log.AppendLine("---");
			Log.AppendLine("---");
			Log.AppendLine("---");
		}				
	};
	audit.IsEnabled = true;
}

// ...code...

using (var context = new EntityContext())
{
	context.Customers.Add(new Customer() { Name = "Customer_A", Description = "Description" });
	context.Customers.Add(new Customer() { Name = "Customer_B", Description = "Description" });			
	context.Customers.Add(new Customer() { Name = "Customer_C", Description = "Description" });

	// Save changes with Audit Enabled
	context.SaveChanges();
}

// Display Audit Trail
Console.WriteLine(Log.ToString());
```
Try it: [NET Core](https://dotnetfiddle.net/Y3F4EY) | [NET Framework](https://dotnetfiddle.net/s7Zk45)

## Real Life Scenarios

### Saving audit trail in a database
Your application need to keep an audit trail of all changes in a database. You can automatically save the audit trail by specifying an `AutoSaveSet`.

```csharp
public class EntityContext : DbContext
{
	public EntityContext() : base(FiddleHelper.GetConnectionStringSqlServer())
	{
		// Add your configuration here
		// Saving audit trail in a database
		this.Configuration.Audit.AutoSaveSet = this.XmlAuditEntries;
		this.Configuration.Audit.IsEnabled = true;
	}
	
	public DbSet<Customer> Customers { get; set; }
	public DbSet<XmlAuditEntry> XmlAuditEntries { get; set; }
}

public static void Main()
{
	// Many changes
	using (var context = new EntityContext())
	{
		context.Customers.Add(new Customer() { Name = "Customer_A", Description = "Description" });
		context.Customers.Add(new Customer() { Name = "Customer_B", Description = "Description" });
		
		// Save changes and Audit trail in database
		context.SaveChanges();
	}
	
	using (var context = new EntityContext())
	{
		var customers = context.Customers.ToList();
		
		context.Customers.Remove(customers.Where(x => x.Name == "Customer_B").First());
		customers.Where(x => x.Name == "Customer_A").First().Description = "updated";
		
		// Save changes and Audit trail in database
		context.SaveChanges();
	}
	
	// Display
	using (var context = new EntityContext())
	{
		// Displaying audit trail history
		var auditEntries = context.XmlAuditEntries.ToAuditEntries();
		FiddleHelper.WriteTable("1 - Audit Entries", auditEntries);
		FiddleHelper.WriteTable("2 - Audit Properties", auditEntries.SelectMany(x => x.Properties));
	}
}
```
Try it: [NET Core](https://dotnetfiddle.net/vsoPmT) | [NET Framework](https://dotnetfiddle.net/8fBiZj)

### Saving audit trail in a log file
Your application need to keep an audit trail of all changes in a log file. You can automatically save the audit trail in a log file by specifying an `AutoSaveAction`.


```csharp
public class EntityContext : DbContext
{
	public EntityContext() : base(FiddleHelper.GetConnectionStringSqlServer())
	{
		// Add your configuration here
		this.Configuration.Audit.AutoSaveAction = (context,auditing) => {
			using (System.IO.StreamWriter file = 
		new System.IO.StreamWriter(@"Log.txt"))
		{
				foreach(var entry in auditing.EntriesXml)
				{
					file.WriteLine("EntitySetName: " + entry.EntitySetName);
					file.WriteLine("EntityTypeName: " + entry.EntityTypeName);
					file.WriteLine("State: " + entry.State);
					file.WriteLine("StateName: " + entry.StateName);
					file.WriteLine("CreatedBy: " + entry.CreatedBy);
					file.WriteLine("CreatedDate: " + entry.CreatedDate);
					file.WriteLine("XmlProperties: ");
					file.WriteLine(entry.XmlProperties);
					file.WriteLine("---");
					file.WriteLine("---");
					file.WriteLine("---");
				}	
			}
		};
		this.Configuration.Audit.IsEnabled = true;
	}
	
	public DbSet<Customer> Customers { get; set; }
}

public static void Main()
{
	using (var context = new EntityContext())
	{
		context.Customers.Add(new Customer() { Name = "Customer_A", Description = "Description" });
		context.Customers.Add(new Customer() { Name = "Customer_B", Description = "Description" });			
		context.Customers.Add(new Customer() { Name = "Customer_C", Description = "Description" });
		
		// Save changes with Audit Enabled
		context.SaveChanges();
	}
	
	// Display Audit Trail in the Log.txt
	string[] lines = System.IO.File.ReadAllLines(@"Log.txt");
	foreach (string line in lines)
	{
		Console.WriteLine(line);
	}
}
```
Try it: [NET Core](https://dotnetfiddle.net/ONWxZL) | [NET Framework](https://dotnetfiddle.net/1JvBQ8)

### Saving audit trail in a different database
Your application need to keep an audit trail of all changes in a different database. You can automatically save the audit trail in a different database file by specifying an `AutoSaveAction`.

```csharp
public class EntityContext : DbContext
{
	public EntityContext() : base(FiddleHelper.GetConnectionStringSqlServer())
	{
		// Add your configuration here			
		this.Configuration.Audit.AutoSaveAction = (context, auditing) => {
			var auditContext = new AuditContext();
			auditContext.XmlAuditEntries.AddRange(auditing.EntriesXml);
			auditContext.SaveChanges();
		};
		
		this.Configuration.Audit.IsEnabled = true;
	}
	
	public DbSet<Customer> Customers { get; set; }
}

public class AuditContext : DbContext
{
	public AuditContext() : base(FiddleHelper.GetConnectionStringSqlServer())
	{
	}

	public DbSet<XmlAuditEntry> XmlAuditEntries { get; set; }
}

public static void Main()
{
	using (var context = new EntityContext())
	{
		context.Customers.Add(new Customer() { Name = "Customer_A", Description = "Description" });
		
		// Save changes and Audit trail in database
		context.SaveChanges();
	}
	
	using(var auditContext = new AuditContext())
	{
		// Display Audit trail
		var auditEntries = auditContext.XmlAuditEntries.ToAuditEntries();
		FiddleHelper.WriteTable("1 - Audit Entries", auditEntries);
		FiddleHelper.WriteTable("2 - Audit Properties", auditEntries.SelectMany(x => x.Properties));
	}
}
```
Try it: [NET Core](https://dotnetfiddle.net/b9dTrD) | [NET Framework](https://dotnetfiddle.net/JMVLsE)

### Displaying audit trail history
Your application need to display the audit trail of all changes in a user interface. If your audit trail is saved in a database, you can retrieve and display audit entries by using your audit `DbSet<>`.

```csharp
// Display
using (var context = new EntityContext())
{
	// Displaying audit trail history
	var auditEntries = context.XmlAuditEntries.ToAuditEntries();
	FiddleHelper.WriteTable("1 - Audit Entries", auditEntries);
	FiddleHelper.WriteTable("2 - Audit Properties", auditEntries.SelectMany(x => x.Properties));
}
```
Try it: [NET Core](https://dotnetfiddle.net/RHEm3C) | [NET Framework](https://dotnetfiddle.net/wfoPB1)

## Documentation

### AuditManager

The `AuditManager` allows you to configure how the audit trail will be created, saved, and retrieved.

###### Properties

| Name | Description | Default | Example |
| :--- | :---------- | :-----: | :------ |
| `IsEnabled` | Gets or sets if the `Audit` feature is enabled. By default, this feature is disabled not to impact the performance. | `false` | [NET Core](https://dotnetfiddle.net/G0wYQp) / [NET Framework](https://dotnetfiddle.net/4xrM1d) |
| `AutoSaveAction` | Gets or sets the `AutoSaveAction`. This option is usually used to automatically save the audit trail in a log file or different database. | `null` | [NET Core](https://dotnetfiddle.net/cn5rnx) / [NET Framework](https://dotnetfiddle.net/YzYyE7) |
| `AutoSaveSet` | Gets or sets the `AutoSaveSet`. This option is usually used to automatically save the audit trail in a database. | `null` | [NET Core](https://dotnetfiddle.net/CTeosc) / [NET Framework](https://dotnetfiddle.net/hXzGVu) |
| `LastAudit` | Gets the last `Audit` trail. | `null` | [NET Core](https://dotnetfiddle.net/R1nGCP) / [NET Framework](https://dotnetfiddle.net/8ce2Y1) |

###### Methods (Display)

| Name | Description | Example |
| :--- | :---------- | :------ |
| `DisplayDBNullAsNull(bool value)` | Format DBNull.Value as 'null' value. True by default. | [NET Core](https://dotnetfiddle.net/tC4a6r) / [NET Framework](https://dotnetfiddle.net/8dawtN) |
| `DisplayFormatType<Type>(Func<Type, string> formatter)` | Format the specified type into a string. | [NET Core](https://dotnetfiddle.net/XQ0VF5) / [NET Framework](https://dotnetfiddle.net/5whaAx) |
| `DisplayEntityName<TEntityType>(string name)` | Use the specified name for the `EntityName` value. | [NET Core](https://dotnetfiddle.net/7ZgRhL) / [NET Framework](https://dotnetfiddle.net/xu4yPs)  |
| `DisplayEntityName<TEntityType>(Func<TEntityType, string, string> formatter)` | Use the factory specified name for the `EntityName` value. | [NET Core](https://dotnetfiddle.net/biZ8DT) / [NET Framework](https://dotnetfiddle.net/01IYPh)  |
| `DisplayPropertyName<TEntityType>(Expression<Func<TEntityType, object>> propertySelector, string name)` | Use the specified name for the `PropertyName` value. | [NET Core](https://dotnetfiddle.net/ZNl7u2) / [NET Framework](https://dotnetfiddle.net/AxkTW3) |
| `DisplayPropertyName<TEntityType>(Expression<Func<TEntityType, object>> propertySelector, Func<TEntityType, string, string> formatter)` | Use the factory specified name for the `PropertyName` value. | [NET Core](https://dotnetfiddle.net/t1x5Cy) / [NET Framework](https://dotnetfiddle.net/BKBSKc) |

###### Methods (Include & Exclude)

| Name | Description | Example |
| :--- | :---------- | :------ |
| `ExcludeEntity()` | Excludes all entities from the audit. | [NET Core](https://dotnetfiddle.net/p8o9UE) / [NET Framework](https://dotnetfiddle.net/MrWtV4) |
| `ExcludeEntity<TEntityType>()` | Excludes all entities of `TEntityType` type from the audit. | [NET Core](https://dotnetfiddle.net/7hEYeC) / [NET Framework](https://dotnetfiddle.net/Nes7be) |
| `ExcludeEntity(Func<object, bool> predicate)` | Excludes all entities that satisfy the predicate from the audit. | [NET Core](https://dotnetfiddle.net/FeAakc) / [NET Framework](https://dotnetfiddle.net/tWTkaC) |
| `ExcludeEntity<TEntityType>(Func<TEntityType, bool> predicate)` | Excludes all entities of `TEntityType` that satisfy the predicate from the audit. | [NET Core](https://dotnetfiddle.net/BwYX8G) / [NET Framework](https://dotnetfiddle.net/TfmtL5) |
| `ExcludeEntity(AuditEntryState entryState)` | Excludes all entities with specified `AuditEntryState` from the audit. | [NET Core](https://dotnetfiddle.net/JQ082S) / [NET Framework](https://dotnetfiddle.net/vLHfk9) |
| `ExcludeEntity<TEntityType>(AuditEntryState entryState)` | Excludes all entities of 'TEntityType' type and with specified `AuditEntryState` from the audit. | [NET Core](https://dotnetfiddle.net/1ZnoT6) / [NET Framework](https://dotnetfiddle.net/g5t6FB) |
| `ExcludeEntity(Func<object, bool> predicate, AuditEntryState entryState)` | Excludes all entities that satisfy the predicate and with specified `AuditEntryState` from the audit. | [NET Core](https://dotnetfiddle.net/JzGX7h) / [NET Framework](https://dotnetfiddle.ne22Zlv) |
| `ExcludeEntity<TEntityType>(Func<TEntityType, bool> predicate, AuditEntryState entryState)` | Excludes all entities of `T / EntityType` that satisfy the predicate and with specified `AuditEntryState` from the audit. | [NET Core](https://dotnetfiddle.net/m9bpsL) / [NET Framework](https://dotnetfiddle.net/g22Zlv) |
| `ExcludeProperty()` | Excludes all properties from the audit. Key properties are never excluded. | [NET Core](https://dotnetfiddle.net/dVdTgS) / [NET Framework](https://dotnetfiddle.net/2KULLj) |
| `ExcludeProperty<TEntityType>()` | Excludes all properties of a `TEntityType` type from the audit. Key properties are never excluded. | [NET Core](https://dotnetfiddle.net/CD9qRo) / [NET Framework](https://dotnetfiddle.net/5XTmn4) |
| `ExcludeProperty<TEntityType>(Expression<Func<TEntityType, object>> propertySelector)` | Excludes specified properties of a `TEntityType` type from the audit. Key properties are never excluded. | [NET Core](https://dotnetfiddle.net/qNo0IN) / [NET Framework](https://dotnetfiddle.net/BlIDQY) |
| `ExcludePropertyUnchanged()` | Excludes all properties value unchanged from the audit. Key values are never excluded. | [NET Core](https://dotnetfiddle.net/DGAmuk) / [NET Framework](https://dotnetfiddle.net/9Aw0vq) |
| `ExcludePropertyUnchanged<TEntityType>()` | Excludes all properties value unchanged of a `TEntityType` type from the audit. Key values are never excluded. | [NET Core](https://dotnetfiddle.net/fgvDZY) / [NET Framework](https://dotnetfiddle.net/EKOk3a) |
| `ExcludePropertyUnchanged(Func<object, bool> predicate)` | Excludes all properties value that satistfy the predicate from the audit. Key values are never excluded. | [NET Core](https://dotnetfiddle.net/wUaFws) / [NET Framework](https://dotnetfiddle.net/qhTX5h) |
| `IncludeEntity()` | Includes all entities in the audit. | [NET Core](https://dotnetfiddle.net/qm0ZFS) / [NET Framework](https://dotnetfiddle.net/r0Rq73) |
| `IncludeEntity<TEntityType>()` | Includes all entities of `TEntityType` type in the audit. | [NET Core](https://dotnetfiddle.net/GqRgNq) / [NET Framework](https://dotnetfiddle.net/rjhUQb) |
| `IncludeEntity(Func<object, bool> predicate)` | Includes all entities that satisfy the predicate in the audit. | [NET Core](https://dotnetfiddle.net/DvNJiW) / [NET Framework](https://dotnetfiddle.net/TdqHmK)  |
| `IncludeEntity<TEntityType>(Func<TEntityType, bool> predicate)` | Includes all entities of `TEntityType` that satisfy the predicate in the audit. | [NET Core](https://dotnetfiddle.net/CQtuiY) / [NET Framework](https://dotnetfiddle.net/KiLBLq)  |
| `IncludeEntity(AuditEntryState entryState)` | Includes all entities with specified `AuditEntryState` in the audit. | [NET Core](https://dotnetfiddle.net/FmTkkV) / [NET Framework](https://dotnetfiddle.net/YA3Gr0) |
| `IncludeEntity<TEntityType>(AuditEntryState entryState)` | Includes all entities of 'TEntityType' type and with specified `AuditEntryState` in the audit. | [NET Core](https://dotnetfiddle.net/xr6PK5) / [NET Framework](https://dotnetfiddle.net/PFY5Pp) |
| `IncludeEntity(Func<object, bool> predicate, AuditEntryState entryState)` | Includes all entities that satisfy the predicate and with specified `AuditEntryState` in the audit. | [NET Core](https://dotnetfiddle.net/NYxFBR) / [NET Framework](https://dotnetfiddle.net/yPchrX) |
| `IncludeEntity<TEntityType>(Func<TEntityType, bool> predicate, AuditEntryState entryState)` | Includes all entities of `TEntityType` that satisfy the predicate and with specified `AuditEntryState` in the audit. | [NET Core](https://dotnetfiddle.net/VhaREm) / [NET Framework](https://dotnetfiddle.net/yPchrX) |
| `IncludeProperty()` | Includes all properties in the audit. | [NET Core](https://dotnetfiddle.net/ZFyTHP) / [NET Framework](https://dotnetfiddle.net/CN9Zq1) |
| `IncludeProperty<TEntityType>()` | Includes all properties of a `TEntityType` type in the audit. | [NET Core](https://dotnetfiddle.net/A0QVNT) / [NET Framework](https://dotnetfiddle.net/e74jy7) |
| `IncludeProperty<TEntityType>(Expression<Func<TEntityType, object>> propertySelector)` | Includes specified properties of a `TEntityType` type in the audit. | [NET Core](https://dotnetfiddle.net/QdwTJd) / [NET Framework](https://dotnetfiddle.net/c7YrfX) |
| `IncludePropertyUnchanged()` | Includes all property values unchanged in the audit. | [NET Core](https://dotnetfiddle.net/aa0gRO) / [NET Framework](https://dotnetfiddle.net/RPfZEl) |
| `IncludePropertyUnchanged<TEntityType>()` | Includes all property values unchanged of a `TEntityType` type in the audit. | [NET Core](https://dotnetfiddle.net/ul8tCJ) / [NET Framework](https://dotnetfiddle.net/bYmNVq) |
| `IncludePropertyUnchanged(Func<object, bool> predicate)` | Includes all property values that satistfy the predicate in the audit. | [NET Core](https://dotnetfiddle.net/Up4qJT) / [NET Framework](https://dotnetfiddle.net/HpJ5mz) |
| `UseIncludeDataAnnotation(bool value)` | Exclude all entities and properties from the Audit. Only entities and properties with `Include` data annotations or specified with the fluent API will be included. | [NET Core](https://dotnetfiddle.net/4ffqFm) / [NET Framework](https://dotnetfiddle.net/mGBM7i) |

###### Methods (Soft Delete)

| Name | Description | Example |
| :--- | :---------- | :------ |
| `SoftDeleted(Func<object, bool> predicate)` | Change the `AuditEntryState` from `EntityModified` to `EntitySoftDeleted` for all entities that satisfy the soft delete predicate. | [NET Core](https://dotnetfiddle.net/OGgoXq) / [NET Framework](https://dotnetfiddle.net/k1h8fr) |
| `SoftDeleted<TEntityType>(Func<TEntityType, bool> predicate)` | Change the `AuditEntryState` from `EntityModified` to `EntitySoftDeleted` for all entities of `TEntityType` type and that satisfy the soft delete predicate. | [NET Core](https://dotnetfiddle.net/qit5zp) / [NET Framework](https://dotnetfiddle.net/OdVFGF) / |

### Audit

The `Audit` class provide information about the audit trail.

###### Properties

| Name | Description | Example |
| :--- | :---------- | :------ |
| `Entries` | Gets a list of `AuditEntry`. | [NET Core](https://dotnetfiddle.net/lUrJyf) / [NET Framework](https://dotnetfiddle.net/In5zMB) |
| `EntriesXml` | Gets a list of `XmlAuditEntry`. | [NET Core](https://dotnetfiddle.net/FKtYmU) / [NET Framework](https://dotnetfiddle.net/9WKMvp) |
| `Manager` | Gets the `AuditManager` | [NET Core](https://dotnetfiddle.net/6zPnr0) / [NET Framework](https://dotnetfiddle.net/LVU9UD) |

### AuditEntry

The `AuditEntry` class contains information about the entry and a list of `AuditEntryProperty`.

###### Properties (Mapped)

This properties values are saved in a database.

| Name | Description | Example |
| :--- | :---------- | :------ |
| `AuditEntryID` | Gets or sets the `AuditEntryID`. | [NET Core](https://dotnetfiddle.net/NlgCch) / [NET Framework](https://dotnetfiddle.net/bn3OpH) |
| `EntitySetName` | Gets or sets the `EntitySet` name. | [NET Core](https://dotnetfiddle.net/1y64Wo) / [NET Framework](https://dotnetfiddle.net/wEjMFB) |
| `EntityTypeName` | Gets or sets the `EntityType` name. | [NET Core](https://dotnetfiddle.net/Xjoa3T) / [NET Framework](https://dotnetfiddle.net/Ulretn) |
| `State` | Gets or sets the `AuditEntryState`. | [NET Core](https://dotnetfiddle.net/qINtEL) / [NET Framework](https://dotnetfiddle.net/hitoCH) |
| `StateName` | Gets or sets the `AuditEntryState` name. | [NET Core](https://dotnetfiddle.net/gjhL3z) / [NET Framework](https://dotnetfiddle.net/hzefDr) |
| `CreatedBy` | Gets or sets the `AuditEntry` created user. | [NET Core](https://dotnetfiddle.net/Hp3A2M) / [NET Framework](https://dotnetfiddle.net/JjBEqS) |
| `CreatedDate` | Gets or sets the `AuditEntry` created date. | [NET Core](https://dotnetfiddle.net/TYP6xQ) / [NET Framework](https://dotnetfiddle.net/XG8s4n) |
| `Properties` | Gets or sets the `AuditEntry` properties. | [NET Core](https://dotnetfiddle.net/wzACXb) / [NET Framework](https://dotnetfiddle.net/FYsJgt) |

###### Properties (Unmapped)

This properties values are only accessible via the `LastAudit` property.

| Name | Description | Example |
| :--- | :---------- | :------ |
| `Parent` | Gets or sets the parent `Audit`. | [NET Core](https://dotnetfiddle.net/f1QLhL) / [NET Framework](https://dotnetfiddle.net/BwOXVR) |
| `Entity` | Gets or sets the audited `Entity`. | [NET Core](https://dotnetfiddle.net/BgcJGB) / [NET Framework](https://dotnetfiddle.net/4QeyV2) |
| `Entry` | Gets or sets the audited `ObjectStateEntry`.  | [NET Core](https://dotnetfiddle.net/TZFcwE) / [NET Framework](https://dotnetfiddle.net/zvfkd3) |

<details>
  <summary>Database First SQL</summary>

```sql
Coming soon...
```
</details>

### AuditEntryProperty

The `AuditEntryProperty` contains information about `property`.

###### Properties (Mapped)

These property values are saved in a database.

| Name | Description | Example |
| :--- | :---------- | :------ |
| `Parent` | Gets or sets the parent `AuditEntry`. | [NET Core](https://dotnetfiddle.net/HGztk6) / [NET Framework](https://dotnetfiddle.net/WXpAtA) |
| `AuditEntryPropertyID` | Gets or sets the `AuditEntryPropertyID`. | [NET Core](https://dotnetfiddle.net/waTLnZ) / [NET Framework](https://dotnetfiddle.net/C8GBWu) |
| `AuditEntryID` | Gets or sets the `AuditEntryID`. | [NET Core](https://dotnetfiddle.net/tRQHR2) / [NET Framework](https://dotnetfiddle.net/kJzQ6i) |
| `RelationName` | Gets or sets the relation name. Only available for `RelationshipAdded` and `RelationshipDeleted` state | [NET Core](https://dotnetfiddle.net/VDJMnu) / [NET Framework](https://dotnetfiddle.net/LTd809) |
| `PropertyName` | Gets or sets the property name. | [NET Core](https://dotnetfiddle.net/1x8YYl) / [NET Framework](https://dotnetfiddle.net/oYqqV0) |
| `OldValue` | Gets or sets the old value formatted as string. Avalable for `Modified`, `Deleted`, and `RelationshipDeleted` state. | [NET Core](https://dotnetfiddle.net/rpZJnR) / [NET Framework](https://dotnetfiddle.net/hNUCx6) |
| `NewValue` | Gets or sets the new value formatted as string. Avalable for `Insert`, `Modified`, and `RelationshipModified` state. | [NET Core](https://dotnetfiddle.net/sE9q1l) / [NET Framework](https://dotnetfiddle.net/lrGw9a) |

###### Properties (Unmapped)

These property values are only accessible via the `LastAudit` property.

| Name | Description | Example |
| :--- | :---------- | :------ |
| `OldValueRaw` | Gets or sets the old raw value. This is the original raw value without being formatted. | [NET Core](https://dotnetfiddle.net/2GQMww) / [NET Framework](https://dotnetfiddle.net/cxK2Hq) |
| `NewValueRaw` | Gets or sets the new raw value. This is the original raw value without being formatted. | [NET Core](https://dotnetfiddle.net/h3f9uu) / [NET Framework](https://dotnetfiddle.net/zetqj9) |

<details>
  <summary>Database First SQL</summary>

```sql
Coming soon...
```
</details>

### XmlAuditEntry

The `XmlAuditEntry` class contains information about the entry and all properties in a `Xml` format.

###### Properties (Mapped)

| Name | Description | Example |
| :--- | :---------- | :------ |
| `XmlAuditEntryID` | Gets or sets the `AuditEntryID`. | [NET Core](https://dotnetfiddle.net/8QgOYK) / [NET Framework](https://dotnetfiddle.net/ohl81V) |
| `EntitySetName` | Gets or sets the `EntitySet` name. | [NET Core](https://dotnetfiddle.net/svSlcX) / [NET Framework](https://dotnetfiddle.net/r1sti8) |
| `EntityTypeName` | Gets or sets the `EntityType` name. | [NET Core](https://dotnetfiddle.net/btlslm) / [NET Framework](https://dotnetfiddle.net/InPE7m) |
| `State` | Gets or sets the `AuditEntryState`. | [NET Core](https://dotnetfiddle.net/JzAA7I) / [NET Framework](https://dotnetfiddle.net/yl2ESG) |
| `StateName` | Gets or sets the `AuditEntryState` name. | [NET Core](https://dotnetfiddle.net/3SIlHS) / [NET Framework](https://dotnetfiddle.net/Gok4r5) |
| `CreatedBy` | Gets or sets the `AuditEntry` created user. | [NET Core](https://dotnetfiddle.net/eI8npG) / [NET Framework](https://dotnetfiddle.net/SooSeu) |
| `CreatedDate` | Gets or sets the `AuditEntry` created date. | [NET Core](https://dotnetfiddle.net/y72ulW) / [NET Framework](https://dotnetfiddle.net/LT6aSE) |
| `XmlProperties` | Gets or sets audit properties formatted as `Xml`. | [NET Core](https://dotnetfiddle.net/74ftKB) / [NET Framework](https://dotnetfiddle.net/Dcldvf) |

<details>
  <summary>Database First SQL</summary>

```sql
Coming soon...
```
</details>

### Data Annotations

###### Entity
| Name | Description | Example |
| :--- | :---------- | :------ |
| `AuditDisplay(string name)` | Attribute to change the Audit entity or property display name. | [NET Core](https://dotnetfiddle.net/iPg6XP) / [NET Framework](https://dotnetfiddle.net/y0wQ4E) |
| `AuditExclude` | Attribute to exclude from the audit the entity or property. | [NET Core](https://dotnetfiddle.net/5T418L) / [NET Framework](https://dotnetfiddle.net/iwD7qc) |
| `AuditInclude` | Attribute to include in the audit the entity or property. Require to enable `Include` with `AuditManager` `UseIncludeDataAnnotation(true)` method. | [NET Core](https://dotnetfiddle.net/kuIqx4) / [NET Framework](https://dotnetfiddle.net/OPkrkn) |


###### Property
| Name | Description | Example |
| :--- | :---------- | :------ |
| `AuditDisplay(string name)` | Attribute to change the Audit entity or property display. | [NET Core](https://dotnetfiddle.net/Ioly6x) / [NET Framework](https://dotnetfiddle.net/ePMX3w) |
| `AuditDisplayFormat(string dataFormatString)` | Attribute to change the Audit property display format. | [NET Core](https://dotnetfiddle.net/rXKLKv) / [NET Framework](https://dotnetfiddle.net/rDWKqR) |
| `AuditExclude` | Attribute to exclude from the audit the entity or property. | [NET Core](https://dotnetfiddle.net/iJSkU1) / [NET Framework](https://dotnetfiddle.net/0caNE1) |
| `AuditInclude` | Attribute to include in the audit the entity or property. Require to enable `Include` with `AuditManager` `UseIncludeDataAnnotation(true)` method.  | [NET Core](https://dotnetfiddle.net/yflmCf) / [NET Framework](https://dotnetfiddle.net/1aE47U) |

### Extension Methods
| Name | Description | Example |
| :--- | :---------- | :------ |
| `Where<TEntityType>(this DbSet<AuditEntry> set)` | Gets the audit trail of all entries entry of `TEntityType` type. | [NET Core](https://dotnetfiddle.net/n5Y5O4) / [NET Framework](https://dotnetfiddle.net/Lm48H8) |
| `Where<TEntityType>(this DbSet<AuditEntry> set, TEntityType entry)` | Gets the audit trail of the specific entry. | [NET Core](https://dotnetfiddle.net/QZ27xs) / [NET Framework](https://dotnetfiddle.net/vSmMSn) |
| `Where<TEntityType>(this DbSet<AuditEntry> set, params object[] keyValues)` | Gets the audit trail of the specific key. | [NET Core](https://dotnetfiddle.net/51mC5p) / [NET Framework](https://dotnetfiddle.net/dbtkiu) |
| `Where<TEntityType>(this DbSet<XmlAuditEntry> set)` | Gets the audit trail of all entries entry of `TEntityType` type. | [NET Core](https://dotnetfiddle.net/QJ1K0y) / [NET Framework](https://dotnetfiddle.net/zIwUYc) |
| `ToAuditEntries(this IEnumerable<XmlAuditEntry> items)` | Return a list of `XmlAuditEntry` converted into `AuditEntry`. | [NET Core](https://dotnetfiddle.net/ocIbKT) / [NET Framework](https://dotnetfiddle.net/dVG40P) |

## Limitations

### Bulk Operations & Batch Operations
All operations that doesn't use the `ChangeTracker` (required to create the audit trail) are currently not supported:
- [Bulk Insert](/bulk-insert)
- [Bulk Update](/bulk-update)
- [Bulk Delete](/bulk-delete)
- [Bulk Merge](/bulk-merge)
- [Bulk Synchronize](/bulk-synchronize)
- [Delete from Query](/delete-from-query)
- [Update from Query](/update-from-query)
