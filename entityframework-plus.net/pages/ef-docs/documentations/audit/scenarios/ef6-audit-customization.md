---
Permalink: ef6-audit-customization
CanonicalLink: https://entityframework-plus.net/ef-core-audit-customization
Name: Audit - Customization
---

# Audit - Customization

## Problem

You want to customize some fields value like CreatedDate or even more, want to use your own class inheriting from AuditEntry && AuditEntryProperty?

## Solution

Use AuditEntryFactory && AuditEntryPropertyFactory

### Customize CreatedDate


{% include template-example.html %} 
```csharp

// using Z.EntityFramework.Plus; // Don't forget to include this.

// Globally
AuditManager.DefaultConfiguration
            .AuditEntryFactory = args => 
			    new AuditEntry() { CreatedDate = DateTime.Now.AddHours(-5) };

// By Instance
var audit = new Audit();
audit.CreatedBy = "ZZZ Projects"; // Optional
audit.Configuration.AuditEntryFactory = args => 
                       new AuditEntry() {CreatedDate = DateTime.Now.AddHours(-5)};
ctx.SaveChanges(audit);

```
[Try it in EF Core](https://dotnetfiddle.net/aVIC0C)

### Custom Class

{% include template-example.html %} 
```csharp

// using Z.EntityFramework.Plus; // Don't forget to include this.

// Globally
AuditManager.DefaultConfiguration
            .AuditEntryFactory = args => 
			    new CustomAuditEntry() { IpAddress = address };

AuditManager.DefaultConfiguration
            .AuditEntryPropertyFactory = args => 
			    new CustomAuditEntryProperty() { CustomField = value };

// By Instance
var audit = new Audit();
audit.CreatedBy = "ZZZ Projects"; // Optional
audit.Configuration.AuditEntryFactory = args => 
			    new CustomAuditEntry() { IpAddress = address };

audit.Configuration.AuditEntryPropertyFactory = args => 
			    new CustomAuditEntryProperty() { CustomField = value };
ctx.SaveChanges(audit);

```
[Try it](https://dotnetfiddle.net/xazeGj)

### Custom DbSet with AutoSave

{% include template-example.html %} 
```csharp

public class EntityContext : DbContext
{
   // ... context code ...
   public DbSet<CustomAuditEntry> CustomAuditEntries { get; set; }
   public DbSet<CustomAuditEntryProperty> CustomAuditEntryProperties { get; set; }
}

AuditManager.DefaultConfiguration.AutoSavePreAction = (context, audit) =>
    // ADD "Where(x => x.AuditEntryID == 0)" to allow multiple SaveChanges with same Audit
    (context as EntityContext).CustomAuditEntries.AddRange(audit.Entries.Cast<CustomAuditEntry>());

var audit = new Audit();
audit.CreatedBy = "ZZZ Projects"; // Optional
ctx.SaveChanges(audit);

```
[Try it](https://dotnetfiddle.net/lC7eWg)
