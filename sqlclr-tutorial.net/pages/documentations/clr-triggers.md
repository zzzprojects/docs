---
PermaID: 100004
Name: CLR Triggers
---

# CLR Triggers

A trigger in SQL Server terms is a piece of logic that gets executed when an action is taken against a table or a view. There are two types of triggers supported in SQL Server; 

 - **DML (Data Manipulation Language):** It can be processed in response to insert, update and delete commands on a table or view.
 - **DDL (Data Definition Language):** It can be processed in response to statements that alter the schema of a database, such as create, alter, and drop.

Triggers and procedures have a lot of similarities, or you can say that Triggers are like stored procedures that are automatically processed in response to some predefined action. But there are some differences like;
 
 - Triggers don't accept input parameters. 
 - Triggers also have extra information available to them that regular stored procedures do not, etc.

The easiest way to create a CLR trigger, select **Project > Add New Item** menu option.

<img src="https://raw.githubusercontent.com/zzzprojects/sqlclr-tutorial/master/docs/images/clr-triggers1.png" alt="clr triggers-1">

Select the **SQL CLR C#** from the list on the left pane, and choose **SQL CLR C# Trigger** in the middle pane. Let's the following method which will be invoked when user insert data to the `Authors` table.

```csharp
public static void InsertAuthorTrigger ()
{
    SqlPipe sqlP = SqlContext.Pipe;
    sqlP.Send("Data inserted to Authors table.");
}
```

But we don't have any `Authors` table in our database, so let's add an `Author` table to the database first using the `CREATE TABLE`.

```sql
CREATE TABLE [dbo].[Authors] (
    [AuthorId] INT            IDENTITY (1, 1) NOT NULL,
    [Name]     NVARCHAR (MAX) NULL,
    CONSTRAINT [PK_dbo.Authors] PRIMARY KEY CLUSTERED ([AuthorId] ASC)
);
``` 

The T-SQL statement creates the trigger in SQL Server, it assumes assembly `SqlClrDemo` is already registered in the current SQL Server database.

```sql
CREATE TRIGGER tri_Insert_Authors
ON Authors
FOR INSERT
AS EXTERNAL NAME SqlClrDemo.Triggers.InsertAuthorTrigger
```

Now to see the trigger in action, let's insert data to the `Authors` table using the following SQL.

```sql
INSERT Authors (Name) values ('Andy')
```

You can see that trigger is invoked and the message that we had written in the trigger is displayed.

<img src="https://raw.githubusercontent.com/zzzprojects/sqlclr-tutorial/master/docs/images/clr-triggers2.png" alt="clr triggers-2">
