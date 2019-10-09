---
PermaID: 1000013
Name: Audit DBContext
---

# AuditDbContext

## Definition

**AuditDbContext** provides entity change auditing for Entity Framework POCO entities. It lets you easily implement change auditing on your application entities.

Instead of using Entity Frameworks DbContext you derive your context from AuditDbContext, which will automatically write change audit records to the database for the registered audit types whenever SaveChanges is called.

## Implementation

Implement IAuditableEntity on your entity, or use the AuditableEntity base class.


```csharp
public class Customer : AuditableEntity
{
    public virtual int CustomerId { get; protected set; }
    public virtual string CustomerName { get; set; }
}
```

Define an audit history entity implementing IAuditEntity, or use the AuditEntry base class.


```csharp
public class CustomerAudit : AuditEntity
{
    public virtual int CustomerAuditId { get; protected set; }
    public virtual int CustomerId { get; private set; }
    public virtual string CustomerName { get; set; }
}
```

Derive you context from AuditDbContext.


```csharp
public class Context : AuditDbContext
{
    public IDbSet<Customer> Customers { get; set; }
    public IDbSet<CustomerAudit> CustomerAudits { get; set; }
    public Context(string conn)
        : base(conn)
    {
    }
}
```

Register the audit types either through app.config. Add each of you auditable classes with their audit entity to the config file.


```csharp
<configSections>
  <section name="entityFramework.Audit" type="EntityFramework.Auditing.AuditConfigurationSection, EntityFramework.Auditing" />
</configSections>
<entityFramework.Audit>
    <entities>
      <add name="EntityFramework.Auditing.Test.Customer, EntityFramework.Auditing.Test" audit="EntityFramework.Auditing.Test.CustomerAudit, EntityFramework.Auditing.Test" />
    </entities>
</entityFramework.Audit>
```

You can also register the audit entities in code using the AuditDbContext.RegisterAuditType method.


```csharp
AuditDbContext.RegisterAuditType(typeof(Customer), typeof(CustomerAudit));
```

## Requirements

### Entity Framework Version

 - Entity Framework 6.x

[Learn more](https://auditdbcontext.codeplex.com/)