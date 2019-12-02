# Save Audit History in a Database

When you are working with certain projects, you might need to track each change made to your database. In this article, we will implement the audit feature using Dapper to keep track of entity objects when we insert/modified/delete them.

To keep things simple, we want to save the following details information in a database.

 - **Id:** Unique log id.
 - **Action:** Action name, such Insert, Delete and Update
 - **TableName:** Name of the table
 - **Values:** All column name, old value & new value separated by the pipe "|" characters. Could be a XML, JSON or any other desired format. 
 - **Date:** DateTime of the operation
 - **UserId:** User that triggered it
 
This is the minimal information and you can change it as per your requirements. Let's create the `AuditLogs` table using the following script which will store the audit logs.

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

We now need one custom class to capture the changed entities from the [DapperPlus audit](../audit.md) feature. So let's add the following `AuditLog` class.

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
```

Now we will write a method to access the `auditEntries` which are eligible to log in the database table.

```csharp
private static void SaveAuditLogs(List<AuditEntry> auditEntries)
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

	var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServer());
	connection.BulkInsert<AuditLog>(auditLogs);
}
```
[Try it](https://dotnetfiddle.net/A3UaWJ)

This method will read all the related information which we want to store from each entity and store them to the `AuditLog` entity, and then it will save all the logs' information into the database.