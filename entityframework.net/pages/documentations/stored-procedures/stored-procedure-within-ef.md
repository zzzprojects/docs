---
PermaID: 1000038
Name: Stored Procedure
---

# Stored Procedure

## How to work with stored procedure within Entity Framework? 

How to call a Stored Procedure from Entity Framework?

### StackOverflow Related Questions

 - [How to call Stored Procedures with EntityFramework?](https://stackoverflow.com/questions/14264750/how-to-call-stored-procedures-with-entityframework)
 - [Insertion order of multiple records in Entity Framework](https://stackoverflow.com/questions/39062972/execute-stored-procedure-using-entity-framework)

## Answer

Entity Framework allows you to use stored procedures to perform predefined logic on database tables. It can also specify that EF should use your stored procedures for inserting, updating, or deleting entities.

 - You can use stored procedures either to get the data or to add/update/delete the records for one or multiple database tables.

We have a very simple database which contains one table, and you can create this table using the following SQL.


```csharp
CREATE TABLE [dbo].[Blogs] (
    [BlogId] INT            IDENTITY (1, 1) NOT NULL,
    [Name]   NVARCHAR (MAX) NULL,
    [Url]    NVARCHAR (MAX) NULL,
    CONSTRAINT [PK_dbo.Blogs] PRIMARY KEY CLUSTERED ([BlogId] ASC)
);
``` 

Select New Query and enter the following code in T-SQL editor to add a stored procedure in your database, which will return the blog based on its Id.


```csharp
IF NOT EXISTS (SELECT * FROM sys.objects WHERE object_id = 
   OBJECT_ID(N'[dbo].[GetBlog]') AND type in (N'P', N'PC'))

BEGIN

   EXEC dbo.sp_executesql @statement = N'
   CREATE PROCEDURE [dbo].[GetBlog]
   @BlogId int
   AS
   SELECT * FROM dbo.Blogs 
   WHERE BlogId = @BlogId
   '
END
GO
``` 

The `GetBlog` stored procedure take `BlogId` as a parameter and will return the Blog.

Execute the SQL command, and you will see a stored procedure **"GetBlog"** is added to your database. Execute the query and refresh your database

<img src="https://raw.githubusercontent.com/zzzprojects/EntityFramework-FAQ/master/docs/images/stored-procedure-in-db.png">

Let's add create a new project and add new item select ADO.NET Entity Data Model. Use EF Designer from database and EF will generate all the required classes for you. You can simply call the GetBlog stored procedure using context.


```csharp
using ( var context = new BloggingContextDbEntities())
{
    var blogId = 1;
    var blog = context.GetBlog(blogId).FirstOrDefault();
}
``` 

It will return the blog where Id is 1.