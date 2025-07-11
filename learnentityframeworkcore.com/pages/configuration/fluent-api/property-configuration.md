---
title: Property Configuration In Entity Framework Core
description: You can configure entity properties via the Entity Framework Core Fluent API. These options are made available through methods on the EntityTypeBuilder.Property type
canonical: /configuration/fluent-api/property-configuration
status: Published
lastmod: 2025-07-11
---

# EF Core Property Mapping

Entity Framework Core provides a range of options for configuring entity properties using the Fluent API. These options are available as methods that can be chained to the `EntityTypeBuilder.Property` method, which is available in a number of versions. Some take the name of the property to be configured as strings:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Entity>().Property(typeof(string), "PropertyName")...
}
```
or 
```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Entity>().Property<string>("PropertyName")....
}
```

 Most often, you will see the version that takes a lambda expression denoting the property to be configured. This option provides strong typing and therefore Intellisense assistance and is the preferred option used throughout this guide:

 ```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<EntityName>().Property(p => p.PropertyName)...
}
```
Methods available for property configuration are detailed below. Note that a number of additional provider-specific methods may also become available depending on the database provider that you use. 

+-------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
|HasAnnotation                                                                              |Provides a means to apply annotations via the Fluent API
+-------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
|[HasColumnName](/configuration/fluent-api/hascolumnname-method)                            |Specifies the name of the database column that the property should map to
+-------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
|[HasColumnType](/configuration/fluent-api/hascolumntype-method)                            |Specifies the data type of the database column that the property should map to
+-------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
|[HasDefaultValue](/configuration/fluent-api/hasdefaultvalue-method)                        |Configures the default value of the database column that the property maps to
+-------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
|[HasDefaultValueSql](/configuration/fluent-api/hasdefaultvaluesql-method)                  |Configures the default value expression for the database column that the property maps to
+-------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
|[HasMaxLength](/configuration/fluent-api/hasmaxlength-method)                              |Specifies the maximum length of data that can be stored for strings or binary data (arrays)
+-------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
|[IsConcurrencyToken](/configuration/fluent-api/isconcurrencytoken-method)                  |Denotes that the property takes part in concurrency management
+-------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
|[IsRequired](/configuration/fluent-api/isrequired-method)                                  |Configures the database column as not nullable
+-------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
|[ValueGeneratedNever](/configuration/fluent-api/valuegeneratednever-method)                |Specifies that the database should not automatically generate values for the property
+-------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
|[ValueGeneratedOnAdd](/configuration/fluent-api/valuegeneratedonadd-method)                |Specifies that values should only be generated automatically when new data is added
+-------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
|[ValueGeneratedOnAddOrUpdate](/configuration/fluent-api/valuegeneratedonaddorupdate-method)|Specifies that values should be generated automatically when data is added or updated
+-------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
