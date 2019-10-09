---
PermaID: 100008
Name: Create CLR Database Objects
---

# Create CLR Database Objects

The most productive way of creating CLR database objects is to use Visual Studio which provides tools that can help you to create CLR database objects as well as deploy and debug them.

To create a CLR database objects in Visual Studio, first select the **File > New > Project...** menu option and then select the **SQL Server Database Project** template.

<img src="https://raw.githubusercontent.com/zzzprojects/sqlclr-tutorial/master/docs/images/create-clr-objects1.png">

Once the project is created, you can add a CLR entity to your project from the **Add New Item** dialog box. Select **Project > Add New Item** menu option.

<img src="https://raw.githubusercontent.com/zzzprojects/sqlclr-tutorial/master/docs/images/create-clr-objects2.png">

Select the **SQL CLR C#** from the list on the left pane, and choose **SQL CLR C# Stored Procedure** in the middle pane. 

After selecting an entity type, a class template for that type will be added to your project and opened in the code editor window. The following example shows a simple CLR stored procedure that returns a text message.

```csharp
using System;
using System.Data;
using System.Data.SqlClient;
using System.Data.SqlTypes;
using Microsoft.SqlServer.Server;

public partial class StoredProcedures
{
    [Microsoft.SqlServer.Server.SqlProcedure]
    public static void PrintText()
    {
        SqlPipe sqlP = SqlContext.Pipe;
        sqlP.Send("This is a first stored procedure using CLR database object");
    }
}
```

In Visual Studio, you can simply select the Deploy option, which takes care of both creating the SQL Server Assembly as well as creating the target database object. 

To do so, let's build the application and then right click on project in Solution Explorer and select the **Publish...** option.

<img src="https://raw.githubusercontent.com/zzzprojects/sqlclr-tutorial/master/docs/images/create-clr-objects3.png">

Specify the target connection string and click **Publish** button and you will see the logs on **Data Tools Operations** pane.

<img src="https://raw.githubusercontent.com/zzzprojects/sqlclr-tutorial/master/docs/images/create-clr-objects4.png">

Once the project is succsefully published you can see the script which has created the assembly and stored procedure by clicking on the **View Script** inside **Data Tools Operations** pane.

You can also view it in **SQL Server Object Explorer**.

<img src="https://raw.githubusercontent.com/zzzprojects/sqlclr-tutorial/master/docs/images/create-clr-objects5.png">

## Manual Create CLR Database Objects

If you are not using Visual Studio, or if you want to perform the deployment process manually, you need to copy the .NET DLL to a common storage location. 

### Create Assembly

You can use T-SQL to execute the `CREATE ASSEMBLY` statement that references the location of the .NET DLL.

```sql
CREATE ASSEMBLY SqlDbCLRDemo 
FROM 'C:\SqlCLRDemo.dll' 
WITH PERMISSION_SET = SAFE
GO
```

Once the Assembly is created you can use the `CREATE PROCEDURE` statement to create the stored procedure. The following example creates `PrintText` stored procedure that uses the **SqlCLRDemo** assembly.

```sql
CREATE PROCEDURE PrintText
AS EXTERNAL NAME SqlClrDemo.StoredProcedures.PrintText
```

It will produce the same result as the Publish option did. Now you can execute the this stored procedure using SQL `EXEC` statement.

```sql
EXEC PrintText
```

when you execute this statement, you will see a text message which is specified in `PrintText` method.

<img src="https://raw.githubusercontent.com/zzzprojects/sqlclr-tutorial/master/docs/images/create-clr-objects6.png">
