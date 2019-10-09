---
PermaID: 1000226
Name: MySQL
---

# MySQL

## Introduction

You can also use `MySQL Connector/Net` which integrates support for Entity Framework Core. `MySql.Data.EntityFrameworkCore` allows Entity Framework Core to be used with MySQL. The provider is maintained as part of the [MySQL](https://dev.mysql.com/) project. 

#### Requirements

 - [Install Connector/Net](https://dev.mysql.com/doc/connector-net/en/connector-net-installation-windows.html)
 - [MySQL Server 5.7 or higher](https://dev.mysql.com/downloads/)

#### How to Use MySQL Database Provider

To use MySQL database provider, the first step is to install [ MySql.Data.EntityFrameworkCore](https://www.nuget.org/packages/MySql.Data.EntityFrameworkCore/) NuGet package. Let's consider a simple model which contains three entities.

Now to use Entity Framework Core with MySQL database, override the OnConfiguring method in the context class and set the MySQL data provider using `UseMySQL` method. 


```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
{
    optionsBuilder.UseMySQL("server=localhost;database=library;user=user;password=password");
}
```

#### Limitations

 - The MySQL Connector/Net implementation of EF Core has no support for Memory-Optimized Tables yet.
 - The following table shows Maximum Length of strings types supported by the MySQL Connector/Net implementation of EF Core.


|Data Type	            |Maximum Length	|.NET Type      |
|:----------------------|:--------------|:--------------|
|CHAR	                |255	        |string         |
|BINARY	                |255	        |byte[]         |
|VARCHAR, VARBINARY     |	65,535	    |string, byte[] |
|TINYBLOB, TINYTEXT	    |255	        |byte[]         | 
|BLOB, TEXT	            |65,535	        |byte[]         |
|MEDIUMBLOB, MEDIUMTEXT	|16,777,215	    |byte[]         |
|LONGBLOB, LONGTEXT	    |4,294,967,295	|byte[]         |
|ENUM               	|65,535	        |string         |
|SET	                |65,535	        |string         |
