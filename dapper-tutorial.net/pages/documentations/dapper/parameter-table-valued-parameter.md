---
Name: Parameter TVP
---

# Dapper - Parameter TVP

## Description

A Table-Valued Parameters (TVP) is a SQL Server feature that lets you pass an entire table as an input parameter for a Stored Procedure or Function.

TVP lets you pass a table to allow you to perform "IN" clause, massive insert, and a lot of more.

Here is an example that will seed customers to our Database:

### Step 1

Let first, create the table.

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

Then create the TVP type.

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

And finally, create the stored procedure that will take the TVP type as a parameter.

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

To use a TVP parameter, we must first need to create a DataTable with the same definition and populate it.

In your parameter list, use the `AsTableValuedParameter` with the TVP type name in parameter to execute the Stored Procedure:

```csharp
var dt = new DataTable();
dt.Columns.Add("Code");
dt.Columns.Add("Name");

for(int i = 0; i < 5; i++) {
    dt.Rows.Add("Code_" + i, "Name_" + i);
}
        
connection.Execute("Customer_Seed", new { Customers = dt.AsTableValuedParameter("TVP_Customer") }, commandType: CommandType.StoredProcedure);
```

Try it: [.NET Core](https://dotnetfiddle.net/qt2QPN) | [.NET Framework](https://dotnetfiddle.net/RwPLBk)