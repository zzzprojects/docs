# Save Audit History in a Log File

When you are working with certain projects, you might need to track each change made to your database. In this article, we will implement the audit logs using Dapper to keep track of entity objects when we insert/modified/delete them.

To keep things simple, we want to save the following details information in a log file.

 - **Id:** Unique log id.
 - **Action:** Action name, such Insert, Delete and Update
 - **TableName:** Name of the table
 - **ColumnName:** Name of the column
 - **OldVale:** Value before operation
 - **NewValue:** Value after operation
 - **Date:** DateTime of the operation
 - **UserId:** User that triggered it
 
This is the minimal information and you can change it as per your requirements. We now need one custom class to capture the changed entities from the [DapperPlus audit](../../options-summary/audit.md) feature. So let's add the following `AuditLog` class.

```csharp
public class AuditLog
{
    public int Id { get; set; }
    public string Action { get; set; }
    public string TableName { get; set; }
    public string ColumnName { get; set; }
    public string OldValue { get; set; }
    public string NewValue { get; set; }
    public DateTime Date { get; set; }
    public string UserId { get; set; }
}

```

Now we will write a method to access the `auditEntries` and write all the information into a log file.

```csharp
private static void SaveAuditLogs(List<AuditEntry> auditEntries)
{
    List<AuditLog> auditLogs = new List<AuditLog>();
    foreach (var audit in auditEntries)
    {
        var action = audit.Action.ToString();
        var values = audit.Values;

        foreach (var val in audit.Values)
        {
            AuditLog log = new AuditLog();
            log.Id = ++LOGID;
            log.Action = audit.Action.ToString();                       
            log.Date = audit.Date;
            log.TableName = audit.TableName;
            log.ColumnName = val.ColumnName;
            log.OldValue = val.OldValue == null ? "" : val.OldValue.ToString();
            log.NewValue = val.NewValue == null ? "" : val.NewValue.ToString();
            log.UserId = "my-user-id";

            auditLogs.Add(log);
        }
    }

    string output = JsonConvert.SerializeObject(auditLogs, Formatting.Indented);

    File.WriteAllText("log.txt", output);

}
```
[Try it](https://dotnetfiddle.net/76nEpp)

This method will read all the related information which we want to store from each entity and store them to the `AuditLog` entity, and then it will save all the information into a log file.
