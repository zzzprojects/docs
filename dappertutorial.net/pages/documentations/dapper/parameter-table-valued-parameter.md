---
Title: Dapper Table-Valued Parameter - Learn About AsTableValuedParameter
MetaDescription: Unlock the power of Dapper with Table-Valued Parameter to improve performance with 'AsTableValuedParameter'. Learn how to pass an entire table for stored procedure or function.
LastMod: 2023-10-20
---

# Dapper Table-Valued Parameter: Discover About AsTableValuedParameter

## Description

A Table-Valued Parameters (TVP) is a SQL Server feature that lets you pass an entire table as an input parameter for a Stored Procedure or Function. One of the best features of Dapper is its support for table-valued parameters. 

 - Table-valued parameters are a great way to improve performance when you're working with large amounts of data. 
 - With table-valued parameters, you can send multiple rows of data to the database in one shot. 
 - This can significantly reduce the amount of time that it takes to execute a query.

To use table-valued parameters with Dapper, you need to create a custom type that maps to the data that you want to insert. 

TVP lets you pass a table to allow you to perform an "IN" clause, a massive insert, and a lot more.

Here is an example that will seed customers to our Database:

### Step 1

Now let's create our first table using Dapper and we will call it `Customer`. This table will have three columns: `CustomerID`, `Name`, and `Code`.

```csharp
connection.Execute(@"
    CREATE TABLE [Customer]
    (
        [CustomerID] [INT] IDENTITY(1,1) NOT NULL,
        [Code] [VARCHAR](20) NULL,
        [Name] [VARCHAR](20) NULL,

        CONSTRAINT [PK_Customer] PRIMARY KEY CLUSTERED 
        (
            [CustomerID] ASC
        )
    )
");
```

### Step 2

To create a TVP, you first need to create a user-defined type. A user-defined type (UDT) is a data type that you can define and use in your database. 

After you have created the UDT, you can create a TVP that uses the UDT as its base type. To do this, you use the `CREATE TYPE` statement as shown in the following example.

```csharp
connection.Execute(@"
    CREATE TYPE TVP_Customer AS TABLE
    (
        [Code] [VARCHAR](20) NULL,
        [Name] [VARCHAR](20) NULL
    )
");
```

### Step 3

And finally, create the stored procedure that will take the TVP type as a parameter. To use a TVP in a stored procedure, you need to declare a parameter of the UDT type, as the following example shows:

```csharp
connection.Execute(@"
    CREATE PROCEDURE Customer_Seed
        @Customers TVP_Customer READONLY
    AS
    BEGIN
        INSERT INTO Customer (Code, Name)
        SELECT Code, Name
        FROM @Customers
    END
");
```

### Step 4

To use a TVP parameter, we must first need to create a `DataTable` with the same definition and populate it.

Now you can execute the stored procedure and pass a TVP as a parameter. The following example creates a TVP that contains five rows of data and passes it to the `Customer_Seed` [stored procedure](/stored-procedure). 

```csharp
var dt = new DataTable();
dt.Columns.Add("Code");
dt.Columns.Add("Name");

for(int i = 0; i < 5; i++) {
    dt.Rows.Add("Code_" + i, "Name_" + i);
}
        
connection.Execute("Customer_Seed", new { Customers = dt.AsTableValuedParameter("TVP_Customer") }, commandType: CommandType.StoredProcedure);
```

In your parameter list, use the `AsTableValuedParameter` with the TVP type name in the parameter to execute the Stored Procedure.

Try it: [.NET Core](https://dotnetfiddle.net/qt2QPN) | [.NET Framework](https://dotnetfiddle.net/RwPLBk)

## Related Articles

- [Dapper - Stored Procedure](/stored-procedure)