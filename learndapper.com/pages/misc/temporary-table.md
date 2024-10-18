---
title: Dapper Temporary Table
description: Dapper supports temporary tables by allowing you to create, insert, and query them using the execute or querying methods. Learn more how to use temporary table
canonical: /misc/temporary-table
status: Published
lastmod: 2024-10-18
---

# Temporary Table With Dapper

Temporary tables are a vital part of SQL Server. They enable database developers to store and manipulate data within the database without having to create permanent tables in the system. 

 - Temporary tables are created in memory, meaning they exist only for a certain period of time until they are dropped, or the session ends. 
 - These tables provide a great way to manipulate data within the database, but they also have some limitations that must be considered when using them. 



Temporary tables in Dapper are a great way to store data temporarily while working in your database. They allow you to quickly create and drop tables and move data between permanent and temporary tables. 

 - Temporary tables can be used for tasks such as creating multiple versions of the same table or staging data before inserting it into the actual table. 
 - They can also be used as an alternative to a view when you need to join tables but don't want the overhead of creating a permanent object. 
 - One of the key benefits of using temporary tables in Dapper is that they are only visible within your current session, so other users will not be able to see them or access the data in them. 
 - This provides a layer of security when working with sensitive data. 
 - Temporary tables are also highly efficient in terms of time and resources since they only exist as long as the session that created them. 

## Dapper Temporary Table

The syntax for creating a temporary table in SQL Server is as follows:

```csharp
CREATE TABLE #TempTableName
( 
    Column1 datatype, 
    Column2 datatype, 
    ... 
); 
```
 
In the above syntax, `#TempTableName` is the name of the temporary table, and `Column1` and `Column2` are the names of two columns in the table. The `datatype` can be any valid SQL Server data type such as `int`, `varchar`, or `nvarchar`. 

Once you have created your temporary table, you can add data using the `INSERT` statement. This statement will allow you to populate your table with whatever data you need for the duration of your connection. 

```csharp
using (var connection = new SqlConnection(connectionString))
{
    await conn.Open();
	
    connection.Execute(@"CREATE TABLE #tmpOrder(orderId int);");
    connection.Execute(@"INSERT INTO #tmpOrder(orderId) VALUES (1);");
	
    return connection.Query<int>(@"SELECT * FROM #tmpOrder;");
}
```

You can also query a temporary table like any other table in the database; however, all changes made to it will be lost after closing the connection. Temporary tables can be a great way to store data temporarily without creating permanent tables in the database. 

Here's an improved version of the text for the "CreateTable Methods" section of Dapper Plus:

## Dapper Plus - CreateTable Methods

The [CreateTable](https://dapper-plus.net/create-table) method in Dapper Plus offers a straightforward approach to creating various types of tables directly from an entity type. It supports all type of table:

- Permanent Table
- Temporary Table
- Global Temporary Table

In addition, it support data annotation

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

[Online Example](https://dotnetfiddle.net/1pjhVa)

Throughout this tutorial, we almost always use the `CreateTable` method to set up our examples, as it's a perfect fit for this kind of scenario.

Moreover, the `CreateTable` method isn't just for creation; you can also populate your tables immediately. By passing a list as the first parameter, you can both create and fill a table in a single operation, as detailed in [CreateTable and Populate](https://dapper-plus.net/create-table#create-table-and-populate). This feature simplifies the process of setting up and utilizing new tables in your applications.

```csharp
var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServer());

var customers = new List<Customer>();
customers.Add(new Customer() { Name = "Jonathan Magnan", Email = "info@zzzprojects.com" });
customers.Add(new Customer() { Name = "ZZZ Projects", Email = "sales@zzzprojects.com" });
customers.Add(new Customer() { Name = "Sara", Email = "sara@zzzprojects.com" });

connection.CreateTable(customers, CreateTableType.Permanent);
```

[Online Example](https://dotnetfiddle.net/LSiOah)