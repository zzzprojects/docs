---
title: Data Annotations Attributes
description: Data Annotations Attributes offer a way to configure Entity Framework Core when the model and the database do not follow convention
canonical: /configuration/data-annotation-attributes
status: Published
lastmod: 2023-02-28
---

# EF Core Data Annotations

Configuration enables you to override EF Core's default behaviour. Configuration can be applied in two ways, using the [Fluent API](/configuration/fluent-api), and through DataAnnotation attributes.

Attributes are a kind of tag that you can place on a class or property to specify metadata about that class or property. Entity Framework Core makes use of attributes defined in the  `System.ComponentModel.DataAnnotations.Schema` and `System.ComponentModel.DataAnnotations` namespaces.

#### System.ComponentModel.DataAnnotations.Schema attributes

| Attribute | Description |
| :-- | :-- |
|[Table](/configuration/data-annotation-attributes/table-attribute)  |The database table and/or schema that a class is mapped to. |
|[Column](/configuration/data-annotation-attributes/column-attribute) |The database column that a property is mapped to. |
|[ForeignKey](/configuration/data-annotation-attributes/foreignkey-attribute)   |Specifies the property is used as a foreign key in a relationship.  |
|[DatabaseGenerated](/configuration/data-annotation-attributes/databasegenerated-attribute)|Specifies how the database generates values for a property. |
|[NotMapped](/configuration/data-annotation-attributes/notmapped-attribute)  |Applied to properties or classes that are to be excluded from database mapping. |
|[InverseProperty](/configuration/data-annotation-attributes/inverseproperty-attribute)|Specifies the inverse of a navigation property |
|[ComplexType](/configuration/data-annotation-attributes/complextype-attribute)  |Denotes that the class is a complex type. *Not currently implemented in EF Core. |



#### System.ComponentModel.Annotations attributes

| Attribute | Description |
| :-- | :-- |
|[Key](/configuration/data-annotation-attributes/key-attribute)   |Identifies one or more properties as a Key |
|[Timestamp](/configuration/data-annotation-attributes/timestamp-attribute)  |Specifies the data type of the database column as `rowversion` |
|[ConcurrencyCheck](/configuration/data-annotation-attributes/concurrencycheck-attribute)  |Specifies that the property is included in concurrency checks |
|[Required](/configuration/data-annotation-attributes/required-attribute)  |Specifies that the property's value is required |
|[MaxLength](/configuration/data-annotation-attributes/maxlength-attribute) |Sets the maximum allowed length of the property value (string or array) |
|[StringLength](/configuration/data-annotation-attributes/stringlength-attribute)  |Sets the maximum allowed length of the property value (string or array) |
