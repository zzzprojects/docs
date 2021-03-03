---
PermaID: 100006
Name: CLR User-Defined Functions
---

# CLR User-Defined Functions

A function is a procedure that returns a value or an object. It can take parameters, perform calculations or other actions, and return a result. 

 - You can write user-defined functions in any .NET Framework programming language, such as C# or VB.NET.
 - The .NET functions typically return .NET types, and SQL CLR functions must return a type supported by SQL Server. 

## Types of User-Defined Functions

 - CLR Scalar-Valued Functions
 - CLR Table-Valued Functions 

### CLR Scalar-Valued Functions

A scalar-valued function (SVF) returns a single value, such as a string, integer, or bit value.

 - The input parameters and the type returned from an SVF can be any of the scalar data types supported by SQL Server, except varchar, char, rowversion, text, ntext, image, timestamp, table, or cursor. 
 - SVFs must ensure a match between the SQL Server data type and .NET data type. 

The easiest way to create a CLR scalar-valued function, select **Project > Add New Item** menu option.

<img src="https://raw.githubusercontent.com/zzzprojects/sqlclr-tutorial/master/docs/images/clr-udf1.png" alt="CLR udf-1">

Select the **SQL CLR C#** from the list on the left pane, and choose **SQL CLR C# User Defined Function** in the middle pane. Let's add the following simple function which returns a string message. 

```csharp
public partial class UserDefinedFunctions
{
    [Microsoft.SqlServer.Server.SqlFunction]
    public static string ScalarUDF()
    {
        return "This is a CLR Scalar-Valued function";
    }
}
```

The `SqlFunction` attribute is used for deployment of your SQL CLR functions. The following SQL statement will create a scalar-valued function.

```sql
CREATE FUNCTION dbo.ScalarUDF( )
RETURNS NVARCHAR (MAX)
AS EXTERNAL NAME SqlClrDemo.UserDefinedFunctions.ScalarUDF
```

### CLR Table-Valued Functions

A table-valued function is a user-defined function that returns a table. SQL Server extends the functionality of table-valued functions by allowing you to define a table-valued function in any managed language. 

 - The SQL CLR table-valued functions (TVFs) are a little bit different from SQL CLR scalar-valued functions, or SQL CLR stored procedures. 
 - It must return a type that implements the .NET interface IEnumerable. 
 - TVFs must also provide a fill row method that converts an element of that type to a collection of scalar values that comprise the corresponding table row.

Let's create a very simple CLR Table-Value function which will search a given directory and returns a list of files information with names matching a search pattern. The `FileData` class will return the file name, size in bytes and its creation time.

```csharp
private class FileData
{
    public string Name;
    public long Size;
    public DateTime CreationTime;
    public FileData(string fileName, long fileSize, DateTime creationTime)
    {
        Name = fileName;
        Size = fileSize;
        CreationTime = creationTime;
    }
}
```

The 'GetFiles' method returns a list of `FileData` with names matching a search pattern.

```csharp
[SqlFunction(FillRowMethodName = "FillRows", TableDefinition = "Name nvarchar(500), Size bigint, CreationTime datetime")]
public static IEnumerable GetFiles(string targetDirectory, string searchPattern)
{
    try
    {
        ArrayList FilePropertiesCollection = new ArrayList();
        DirectoryInfo dirInfo = new DirectoryInfo(targetDirectory);
        FileInfo[] files = dirInfo.GetFiles(searchPattern);
        foreach (FileInfo fileInfo in files)
        {
            FilePropertiesCollection.Add(new FileData(fileInfo.Name, fileInfo.Length, fileInfo.CreationTime));
        }
        return FilePropertiesCollection;
    }
    catch (Exception ex)
    {
        return null;
    }
}
```

 - The `FillRowMethodName` property is set to **"FillRows"**. The `FillRows` method will be used to fill a row of data in the table returned by the function. 
 - The `TableDefinition` property sets the structure of the table returned by a table-valued function. For example, this function will return the name of the file, its size in bytes and its creation time. 

Here is code for `FillRows` method, it takes an object of the `FileData` type and breaks it into the file name, size and creation time.

```csharp
private static void FillRows(object objFileProperties, out string fileName, out long fileSize, out DateTime creationTime)
{
    FileData fileProperties = (FileData)objFileProperties;
    fileName = fileProperties.Name;
    fileSize = fileProperties.Size;
    creationTime = fileProperties.CreationTime;
}
```

In Solution Explorer, right click on the project, and select **Properties**.

<img src="https://raw.githubusercontent.com/zzzprojects/sqlclr-tutorial/master/docs/images/clr-udf2.png" alt="CLR udf-2">

On the SQL CLR page set the Permission Level to `EXTERNAL_ACCESS` and then switch to Properties Settings page.

<img src="https://raw.githubusercontent.com/zzzprojects/sqlclr-tutorial/master/docs/images/clr-udf3.png" alt="CLR udf-3">

Click on the **Database Settings...** button and check the Trustworthy option. 

<img src="https://raw.githubusercontent.com/zzzprojects/sqlclr-tutorial/master/docs/images/clr-udf4.png" alt="CLR udf-4">

Let's execute the following SQL.

```sql
SELECT * FROM 
dbo.GetFiles('D:\Project\ZZZ\octokit.net', '*.md')
```

Once it is executed, the Table-Valved function will return the list of files with `*.md` extension.

<img src="https://raw.githubusercontent.com/zzzprojects/sqlclr-tutorial/master/docs/images/clr-udf5.png">
