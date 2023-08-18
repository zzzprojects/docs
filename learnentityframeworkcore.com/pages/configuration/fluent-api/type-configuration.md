---
title: Type configuration in Entity Framework Core
description: Entity Framework Core provides a range of options for configuring types (entities) using the Fluent API
canonical: /configuration/fluent-api/type-configuration
status: Published
lastmod: 2023-02-27
---

# EF Core Type configuration

Entity Framework Core provides a range of options for configuring types (entities) using the Fluent API. These options are available as methods that can be chained to the `ModelBuilder.Entity()` method, which is available in generic and non-generic versions. The non-generic methods take the name or type of the entity to be configured:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity(typeof(EntityName))...
}
```
or 
```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity("EntityName")...
}
```

 Most often, you will use the generic version to specify the type that the configuration applies to. This option is the one that is used throughout this guide:

 ```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<EntityName>()...
}
```

Methods available for type configuration are detailed below.
+----------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------+
|[HasAlternateKey](/configuration/fluent-api/hasalternatekey-method)   |Generates a unique constraint for the specified property or properties
+----------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------+
|HasAnnotation                                                         |Provides a means to apply annotations via the Fluent API
+----------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------+
|HasBaseType                                                           |Specifies the base type of the entity
+----------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------+
|[HasIndex](/configuration/fluent-api/hasindex-method)                 |Generates an index on the specified property or properties
+----------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------+
|[HasKey](/configuration/fluent-api/haskey-method)                     |Denotes the specified property as the entity key 
+----------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------+
|[HasMany](/configuration/fluent-api/hasmany-method)                   |Specifies the Many end of a relationship
+----------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------+
|[HasOne](/configuration/fluent-api/hasone-method)                     |Specifies the One end of a relationship
+----------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------+
|[Ignore](/configuration/fluent-api/ignore-method)                |Denotes that the entity should be omitted from mapping
+----------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------+
|[ToTable](/configuration/fluent-api/totable-method)                   |Specifies the database table that the entity should be mapped to
+----------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------+
|[Property](/configuration/fluent-api/property-configuration)          |Provides access to property configuration
+----------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------+
