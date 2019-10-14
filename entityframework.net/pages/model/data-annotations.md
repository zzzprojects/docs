---
PermaID: 1000125
Name: Data Annotations
---

# Data Annotations

In .NET Framework, data annotation add extra meaning to the data by adding attribute tags. It is used to configure the classes which will highlight the most commonly needed configurations.

 - Data Annotations attributes are .NET attributes which can be applied to an entity class or properties to override default CodeFirst conventions in EF6 and EF Core.
 - The advantage of using Data Annotation feature is that by applying Data Attributes, we can manage the data definition in a single place and do not need re-write the same rules in multiple places.
 - It can be used in a number of .NET applications, such as ASP.NET MVC which allows these applications to leverage the same annotations for client-side validations.

The mostly used Data Annotations attributes are as follows;

|Attribute     	|Description    |
|:------------- |:--------------|
|<a href="{{ site.github.url }}/key">Key</a> |To make the corresponding column a primary pey (PK) column in the database.|
|<a href="{{ site.github.url }}/foreign-key">ForeignKey</a> |Specifies that the property is the foreign key in a relationship.|
|<a href="{{ site.github.url }}/required">Required</a> |To make the corresponding column a `NOT NULL` column in a database table.|
|<a href="{{ site.github.url }}/min-length">MinLength</a> |Specify a minimum number of characters or bytes for the column that the property should map to.|
|<a href="{{ site.github.url }}/max-length">MaxLength</a> |Specify a maximum number of characters or bytes for the column that the property should map to.|
|<a href="{{ site.github.url }}/string-length">StringLength</a> |Sets the maximum allowed length of the property value.|
|<a href="{{ site.github.url }}/table">Table</a> |Create a table with a specified name in Table attribute for a given domain class.|
|<a href="{{ site.github.url }}/column">Column</a> |Create a column with a specified name in Column attribute for a given property in a domain class.|
|<a href="{{ site.github.url }}/concurrency-check">ConcurrencyCheck</a> |Specifies that the property is included in concurrency checks.|
|<a href="{{ site.github.url }}/time-stamp">Timestamp</a> |Specifies the data type of the database column as rowversion.|
|<a href="{{ site.github.url }}/not-mapped">NotMapped</a> |Applied to properties or classes that are to be excluded from database mapping.|
|<a href="{{ site.github.url }}/inverse-property">InverseProperty</a> |Specifies the inverse of a navigation property.|
|<a href="{{ site.github.url }}/complex-type">ComplexType</a> |Complex Types cannot be tracked on their own but they are tracked as part of an entity. |
|<a href="{{ site.github.url }}/database-generated">DatabaseGenerated</a> |Specifies how the database generates values for the property.|