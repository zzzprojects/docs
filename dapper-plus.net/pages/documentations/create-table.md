---
Title: Dapper Create Table | Use Code First approach in your application
MetaDescription: Explore how to utilize Dapper Plus' CreateTable extension method for a 'Code First' approach in your SQL Server applications. Learn about table types, data annotations, and populating tables swiftly. Ideal for both testing scenarios and specific production use cases.
LastMod: 2024-09-21
---

# Dapper Create Table: Use Code First approach in your application

The Dapper Plus `CreateTable` extension method lets you create a table in a SQL Server database. The `CreateTable` method returns the name of the table created.

```csharp
var tableName = connection.CreateTable<Customer>(CreateTableType.Temporary);
```

Using the `CreateTableType` enum, you can pick the type of table:

- **Permanent**: A regular table in your database.
- **Temporary**: A temporary table is a local table made in the `tempdb` database that exists only during the connection (`#mytablename`).
- **GlobalTemporary**: A global temporary table is like a temporary table, but all sessions can access it (`##mytablename`) and is dropped when all sessions that access it are finished.

Feel free to create `Temporary` or `GlobalTemporary` tables on your production server. However, unlike EF Core's true `Code First` approach, we don't recommend using this method to create permanent tables in a production database. There are better ways to achieve that! Nevertheless, this method is excellent for quickly setting up tables for testing or on [.NET Fiddle](https://dotnetfiddle.net/).

In addition, a connection must be open when creating a `Temporary` or `GlobalTemporary` table since those table only live during the connection.

## Create Table

The `CreateTable` extends your `IDbConnection` to let you create a table in a SQL Server database from your entity type.

There are 7 extension methods:

- `CreateTable<T>(this IDbConnection connection)`
- `CreateTable<T>(this IDbConnection connection, string tableName)`
- `CreateTable<T>(this IDbConnection connection, CreateTableType createTableType)`
- `CreateTable(this IDbConnection connection, Type type)`
- `CreateTable(this IDbConnection connection, Type type, string tableName)`
- `CreateTable(this IDbConnection connection, Type type, CreateTableType createTableType)`
- `CreateTable(this IDbConnection connection, Type type, string tableName, CreateTableType createTableType)`

In these methods, you can find the following parts:

- **&lt;T&gt;**: The entity type linked to the table to be created (passed via generic parameter).
- **IDbConnection connection**: The connection to extend with the `CreateTable` method.
- **string tableName**: The table's name to be created.
- **CreateTableType createTableType**: The table's type to be created (`Permanent`, `Temporary`, `GlobalTemporary`).
- **Type type**: The entity type linked to the table to be created (passed via parameter).

```csharp
public static void Main()
{
	var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServer());
	
	// Required only for `Temporary` and `GlobalTemporary` as the name only live during the session.
	connection.Open();
	
	var tableName = connection.CreateTable<Customer>(CreateTableType.Temporary);
	Console.WriteLine(tableName);
}

public class Customer 
{
	public int CustomerID { get; set; }
	public string Name { get; set; }
	public string Email { get; set; }
}
```

[Try it](https://dotnetfiddle.net/d9VMcX)

## Create Table and Data Annotation

Like EF Core, the `CreateTable` from Dapper Plus supports most data annotations:

- **Table**: To specify the `SchemaName` and `TableName`
- **Column**: To name the column and the type.
- **Key**: To note the column is part of the table's `primary key`.
- **DatabaseGenerated**: To indicate if the column should be an `identity` column.
- **Timestamp**: To show a column should be used for versioning and is of type `rowversion`.
- **NotMapped**: To exclude a column from the table creation

```csharp
public static void Main()
{
	var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServer());
	
	connection.CreateTable<Customer>();
}
	
[Table("CustomerWithAnnotation")]
public class Customer 
{
	[Key]
	[DatabaseGenerated(DatabaseGeneratedOption.Identity)]
	public int CustomerID { get; set; }
	[Column("FullName", TypeName="nvarchar(50)")]
	public string Name { get; set; }
	public string Email { get; set; }
	
	[Timestamp]
	public byte[] Version { get; set; }
	
	[NotMapped]
	public string NotMapped { get; set; }
}
```

[Try it](https://dotnetfiddle.net/1pjhVa)

## Create Table and Populate

Besides creating a table, you can also populate it with a list right after you make it. However this part require a valid [Dapper Plus License](/pricing) as a [Bulk Insert](/bulk-insert) is used to populate it:

- `CreateTable<T>(this IDbConnection connection, IEnumerable<T> items, string tableName)`
- `CreateTable<T>(this IDbConnection connection, IEnumerable<T> items, CreateTableType createTableType)`

```csharp
public static void Main()
{
	var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServer());
	
	var customers = new List<Customer>();
	customers.Add(new Customer() { Name = "Jonathan Magnan", Email = "info@zzzprojects.com" });
	customers.Add(new Customer() { Name = "ZZZ Projects", Email = "sales@zzzprojects.com" });
	customers.Add(new Customer() { Name = "Sara", Email = "sara@zzzprojects.com" });
	
	connection.CreateTable(customers, CreateTableType.Permanent);
}

[Table("CustomerWithAnnotation")]
public class Customer 
{
	[Key]
	[DatabaseGenerated(DatabaseGeneratedOption.Identity)]
	public int CustomerID { get; set; }
	[Column("FullName", TypeName="nvarchar(50)")]
	public string Name { get; set; }
	public string Email { get; set; }
	
	[Timestamp]
	public byte[] Version { get; set; }
	
	[NotMapped]
	public string NotMapped { get; set; }
}
```

[Try it](https://dotnetfiddle.net/LSiOah)

## Conclusion

The Dapper Plus create table feature is one of the most valuable free methods our library offers, especially for testing purposes. The ability to quickly set up a database without manually scripting the create statement significantly streamlines development processes.

However, its utility extends beyond just development environments. The need to create and populate temporary tables in production environments is a common scenario, making this feature equally essential for live applications.