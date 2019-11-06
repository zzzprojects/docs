# AuditActionType

## Definition

The `AuditEntries` property which is of type `List<AuditEntry>` gets `UPDATED`, `INSERTED` and `DELETED` data from the database when `UseAudit` is enabled.

The following example sets `UseAudit` to `true` and assigns the list of `AuditEntries` to populate.

```csharp
TBD
```
Try it: [.NET Framework](https://dotnetfiddle.net/XB5npF) | [.NET Core](https://dotnetfiddle.net/y4w1ZG)

## Purpose
Logging old and new values are often useful to keep a history of changes in the database or file.

## FAQ

### Why enabling this option decreases the performance?
Enabling this option will require additional data to be returned from the database.
