---
title: The NotMapped Attribute
description: Usage of the NotMapped Attribute in Entity Framework Core
canonical: /configuration/data-annotation-attributes/notmapped-attribute
status: Published
lastmod: 2023-02-28
---

# EF Core NotMapped Attribute

The `NotMapped` attribute is used to specify that an entity or property is not to be mapped to a table or column in the database. In the following example, the `AuditLog` class will not be mapped to a table in the database:

```csharp
public class Contact
{
    public int ContactId { get; set; }
    public string FirstName { get; set; }
    public string LastName { get; set; }
    public string Email { get; set; } 
    public AuditLog AuditLog { get; set; }
}

[NotMapped]
public class AuditLog
{
    public int EntityId { get; set; }
    public int UserId { get; set; }
    public DateTime Modified { get; set; }
}
```
In this example, the `FullName` property will not be mapped to a column in the Contacts table in the database:
```csharp
public class Contact
{
    public int ContactId { get; set; }
    public string FirstName { get; set; }
    public string LastName { get; set; }
    [NotMapped]
    public string FullName => $"{FirstName} {LastName}";
    public string Email { get; set; } 
}
```
#### Fluent API

The Fluent API equivalent to the `NotMapped` attribute is the [Ignore method](/configuration/fluent-api/ignore-method).
