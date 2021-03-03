---
PermaID: 100001
Name: CLR Database Object Components
---

# CLR Database Object Components

To create .NET database objects, you write managed code in any one of the .NET languages (VB, C#, or Managed C++), and compile it into a .NET DLL. 

 - The most common way to do this is to use Visual Studio to create a new SQL Server project and then build that project, which creates the DLL. 
 - Once the .NET DLL has been created, you need to register that DLL with SQL Server, creating a new SQL Server database object called an assembly.  
 - Then you can create a new database object such as a stored procedure or a trigger that points to the SQL Server assembly.

<img src="https://raw.githubusercontent.com/zzzprojects/sqlclr-tutorial/master/docs/images/clr-database-object-components.png" alt="CLR database object components">

After the coding for the CLR object has been completed, you can use the T-SQL to create a SQL Server assembly

```sql
CREATE ASSEMBLY SqlDbCLRDemo 
FROM 'C:\SqlCLRDemo.dll' 
WITH PERMISSION_SET = SAFE
GO
```

Once the SQL Server assembly is created, you can then use T-SQL to create CLR database objects.

```sql
CREATE PROCEDURE usp_HelloWorld
AS EXTERNAL NAME SqlCLRDemo.StoredProcedures.HelloWorld
GO
```

`CREATE PROCEDURE` statement creates a stored procedure **usp_HelloWorld** that uses the `EXTERNAL NAME` clause to point to the assembly that you created earlier.
