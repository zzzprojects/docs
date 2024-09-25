---
Title: Dapper Plus Manager - Customize for Your Needs
MetaDescription: Learn about the DapperPlusManager, which offers shortcuts, methods for adding custom types, value converters, clearing the cache, and managing licensing.
LastMod: 2024-06-27
---

# Dapper Plus Manager

## Default Context

The properties listed below are essentially shortcuts to simplify access to the corresponding methods in the `DapperPlusManager.DefaultContext`. Instead of using a long form like `DapperPlusManager.DefaultContext.Entity<Product>()`, you can directly use `DapperPlusManager.Entity<Product>()`.

For more detailed information about these properties, please refer to the [Dapper Plus Context](dapper-plus-context).

- **DefaultContext:** This refers to the [Global Context](/dapper-plus-context#global-context).
- **ThrowErrorIfNotMapped:** A shortcut to `DapperPlusManager.DefaultContext.ThrowErrorIfNotMapped`.
- **IsMapperLocked:** A shortcut to `DapperPlusManager.DefaultContext.IsMapperLocked`.
- **MapperCache:** A shortcut to `DapperPlusManager.DefaultContext.MapperCache`.
- **MapperFactory:** A shortcut to `DapperPlusManager.DefaultContext.MapperFactory`.
- **Entity:** A shortcut for accessing `DapperPlusManager.DefaultContext.Entity<T>()` and its corresponding overloads.
- **RemoveEntity:** A shortcut for invoking `DapperPlusManager.DefaultContext.RemoveEntity<T>()` and its corresponding overloads.
- **IsMapped:** A shortcut to `DapperPlusManager.DefaultContext.IsMapped()`.
- **IsMappedEntity:** A shortcut for `DapperPlusManager.DefaultContext.IsMappedEntity<T>()` and its corresponding overloads.

These shortcuts are designed to make your coding experience more efficient by reducing the complexity of frequently used commands.

## Supported Types

By default, our library is equipped to handle a wide range of basic types that suffice for 99.9% of scenarios. However, for those instances where more specialized types are needed, our library provides methods to extend its capabilities:

- **AddCustomSupportedType:** Similar to executing `CustomSupportedTypes.Add(type);`
- **CustomSupportedTypes:** Use this to add a specific type: `CustomSupportedTypes.Add(typeof(MyCustomType));`
- **CustomSupportedTypeNames:** Use this to add a specific type by name: `CustomSupportedTypeNames.Add(typeof(MyCustomType).FullName);`

Below is the logic defining the types we currently support:

```csharp
var isSupported = type == typeof(bool)
				  || type == typeof(char)
				  || type == typeof(sbyte)
				  || type == typeof(byte)
				  || type == typeof(short)
				  || type == typeof(ushort)
				  || type == typeof(int)
				  || type == typeof(uint)
				  || type == typeof(long)
				  || type == typeof(ulong)
				  || type == typeof(float)
				  || type == typeof(double)
				  || type == typeof(decimal)
				  || type == typeof(string)
				  || type == typeof(DateTime)
				  || type == typeof(DateTimeOffset)
				  || type == typeof(TimeSpan)
				  || type == typeof(Guid)
				  || type == typeof(byte[])
				  || type == typeof(TimeOnly)
				  || type == typeof(DateOnly)
				  || (isPostgreSQL
					  && (
						  type == typeof(bool[])
						  || type == typeof(char[])
						  || type == typeof(sbyte[])
						  || type == typeof(byte[])
						  || type == typeof(short[])
						  || type == typeof(ushort[])
						  || type == typeof(int[])
						  || type == typeof(uint[])
						  || type == typeof(long[])
						  || type == typeof(ulong[])
						  || type == typeof(float[])
						  || type == typeof(double[])
						  || type == typeof(decimal[])
						  || type == typeof(string[])
						  || type == typeof(DateTime[])
						  || type == typeof(DateTimeOffset[])
						  || type == typeof(TimeSpan[])
						  || type == typeof(Guid[])
					  ))
				  || type.IsEnumCore()
				  || type.FullName == "Microsoft.SqlServer.Types.SqlGeometry"
				  || type.FullName == "Microsoft.SqlServer.Types.SqlGeography"
				  || type.FullName == "Microsoft.SqlServer.Types.SqlHierarchyId"
				  || type.FullName == "System.Data.Entity.Spatial.DbGeometry"
				  || type.FullName == "System.Data.Entity.Spatial.DbGeography"
				  || type.FullName == "System.Data.Entity.Hierarchy.HierarchyId"
				  || type.FullName == "NetTopologySuite.Geometries.Point"
				  || type.FullName == "NetTopologySuite.Geometries.Geometry"
				  || type.FullName == "NodaTime.LocalDateTime"
				  || type.FullName == "NodaTime.LocalDate"
				  || type.FullName == "NodaTime.ZonedDateTime";
```

## Value Converter

While adding custom types to our library expands its functionality, sometimes mere inclusion isn't enough. We also need to understand how to save and retrieve values for these types effectively. For an illustrative example of this in action, refer to this [GitHub issue](https://github.com/zzzprojects/Dapper-Plus/issues/107#issuecomment-1097482891).

To address this, we provide methods to implement custom logic for value conversion:

- **AddValueConverter(Type type, IBulkValueConverter valueConverter):** This method enables you to apply custom logic that can be used across multiple types. You will need to call this method for each type to which you want this custom logic applied.
- **AddValueConverter\<T>(BulkValueConverter\<T> valueConverter):** This method is specifically designed to apply precise logic for a single type, T.

## Clear Cache

Our library caches multiple pieces of information, including details retrieved from your database tables. Sometimes, developers intentionally modify their database by adding or removing a temporary column, which can lead to issues if our library expects or does not expect this column. Clearing the cache ensures that our library fetches the latest information from the database again.

- **ClearInformationTableCache:** Clears all caches related to information retrieved from the database.
- **ClearAllCache:** Performs the same action as ClearInformationTableCache but additionally clears everything—including [mapping](/mapping) and [options](options). This is done by creating a new instance of [DapperPlusContext](/dapper-plus-context) for the default context: `DapperPlusManager.DefaultContext = new DapperPlusContext();`.

## License

The `DapperPlusManager` is where you add your [license](/licensing) after purchasing the library. We provides two primary methods for license management:

- **AddLicense:** Use this method to manually add a license by hardcoding it into your project or retrieving it from your configuration file.
- **ValidateLicense:** This crucial method ensures that your license has been correctly added and is valid.

## Conclusion

Here's a polished version of your conclusion to ensure clarity and conciseness:

## Conclusion

The `DapperPlusManager` offers a variety of methods and properties, but typically, developers only need to familiarize themselves with three key functionalities:

- Entity<T>()
- AddLicense(licenseName, licenseKey)
- ValidateLicense(out errorMessage)