---
Name: Save Audit History in a Database
LastMod: 2023-03-01
---

# Save Audit History in a Database

When you are working with certain projects, you might need to track each change made to your database. In this article, we will implement the audit feature using Entity Framework to keep track of entity objects when we insert/modified/delete them.

To keep things simple, we want to save the following details information in a database.

 - **Id:** Unique log id.
 - **Action:** Action name, such Insert, Delete and Update
 - **TableName:** Name of the table
 - **Values:** All column name, old value & new value separated by the pipe "|" characters. Could be a XML, JSON or any other desired format. 
 - **Date:** DateTime of the operation
 - **UserId:** User that triggered it

## Code First
Create and add the following `AuditLog` class to your context. This is the minimal information and you can change it as per your requirements 

```csharp
public class AuditLog
{
	public int Id { get; set; }
	public string Action { get; set; }
	public string TableName { get; set; }
	public string Values { get; set; }
	public DateTime Date { get; set; }
	public string UserId { get; set; }
}

public class EntityContext : DbContext
{
	// ...code...
	
	public DbSet<AuditLog> AuditLogs { get; set; }
}
```

## Database First
Create the `AuditLogs` table using the following script which will store the audit logs. This is the minimal information and you can change it as per your requirements.

```sql
CREATE TABLE [dbo].[AuditLog] (
    [Id]         INT            IDENTITY (1, 1) NOT NULL,
    [Action]     NVARCHAR (MAX) NULL,
    [TableName]  NVARCHAR (MAX) NULL,
    [Values]     NVARCHAR (MAX) NULL,
    [Date]       DATETIME       NOT NULL,
    [UserId]     NVARCHAR (MAX) NULL,
    CONSTRAINT [PK_dbo.AuditLogs] PRIMARY KEY CLUSTERED ([Id] ASC)
);
```

## Saving Audits

Now we will write a method to insert audit logs:

```csharp
public static void SaveAuditChanges(List<AuditEntry> auditEntries)
{
	List<AuditLog> auditLogs = new List<AuditLog>();

	foreach (var auditEntry in auditEntries)
	{
		AuditLog log = new AuditLog();
		log.Action = auditEntry.Action.ToString();                       
		log.TableName = auditEntry.TableName;
		log.Values = string.Join("|", auditEntry.Values.Select(x => x.ColumnName + ";" + (x.OldValue ?? "") + ";" + (x.NewValue ?? "")));
		log.Date = auditEntry.Date;
		log.UserId = "my-user-id";
		auditLogs.Add(log);
	}

	var context = new EntityContext();
	
	// we turn off `AutoMapOutputDirection` as we don't need to return the identity values
	context.BulkInsert(auditLogs, options => options.AutoMapOutputDirection = false);
}		
```

and use this method:

```csharp
using (var context = new EntityContext())
{
	var auditEntries = new List<AuditEntry>();
	
	context.BulkInsert(customers, options => {
		options.UseAudit = true;
		options.AuditEntries = auditEntries;
	});
	
	SaveAuditChanges(auditEntries);
	
	FiddleHelper.WriteTable("Saved Audit", context.AuditLogs);
}	
```
[Try it](https://dotnetfiddle.net/qN6MpI)