---
PermaID: 100005
Name: CLR User-defined Aggregates
---

# CLR User-defined Aggregates

Aggregate functions perform a calculation on a set of values and return a single value. T-SQL has a number of built-in aggregates, such as SUM, AVG, and MAX, etc. 

 - The SQL CLR features in SQL Server allow us to implement user-defined aggregates in .NET code and use them from T-SQL. 
 - At first, aggregates look and feel like functions because they accept and return values.
 - The difference is that the argument passed to an aggregate is typically a column, and so each discrete value for that column gets passed in to the aggregate. 
 - It is the aggregate's job to update a variable, which eventually will be the return value, as each discrete value is passed to it.

## Requirements

Aggregate classes must have the following four methods:

 - Init
 - Accumulate
 - Merge
 - Terminate

### Init

 - The `Init` method is used to clean up as necessary from previous uses of this aggregate instance, allowing it to re-start a new aggregate computation.
 - It provides startup code, typically initializing a class-level private variable that will be used by the `Accumulate` method.

### Accumulate

 - The `Accumulate` method accepts a SQL type for processing.
 - It handles the processing of a discrete value into the aggregate value.

### Terminate

 - The `Terminate` method returns a SQL type representing the result.
 - It returns the final aggregated value after all discrete values have been processed.

### Merge

 - The `Merge` method accepts an object typed as the aggregate class itself so that it can be combined with the executing instance.

To create a CLR user-defined type, select **Project > Add New Item** menu option.

<img src="https://raw.githubusercontent.com/zzzprojects/sqlclr-tutorial/master/docs/images/clr-aggregates1.png" alt="clr aggregates-1">

Select the **SQL CLR C#** from the list on the left pane, and choose **SQL CLR C# Aggregate** in the middle pane. It will add the default code for you, let's add the following code.

```csharp
using System;
using System.Data;
using System.Data.SqlClient;
using System.Data.SqlTypes;
using Microsoft.SqlServer.Server;

[Serializable]
[SqlUserDefinedAggregate(Format.Native)]
public struct MaxVariance
{
    //  This is a place-holder field member
    private int m_LowValue;
    private int m_HighValue;

    public void Init()
    {
        m_LowValue = 999999999;
        m_HighValue = -999999999;
    }

    public void Accumulate(int value)
    {
        if ((value > m_HighValue))
        {
            m_HighValue = value;
        }

        if ((value < m_LowValue))
        {
            m_LowValue = value;
        }
    }

    public void Merge(MaxVariance Group)
    {
        if ((Group.GetHighValue() > m_HighValue))
        {
            m_HighValue = Group.GetHighValue();
        }

        if ((Group.GetLowValue() < m_LowValue))
        {
            m_LowValue = Group.GetLowValue();
        }
    }

    public int Terminate()
    {
        return (m_HighValue - m_LowValue);
    }

    //  Helper methods
    private int GetLowValue()
    {
        return m_LowValue;
    }

    private int GetHighValue()
    {
        return m_HighValue;
    }
}

```

The MaxVariance aggregate calculate the difference between high and low values on any column. Let's build the application and Publish it or use the following T-SQL to register this aggregate.

```sql
CREATE AGGREGATE [dbo].[MaxVariance](@value INT NULL)
RETURNS INT
EXTERNAL NAME [SqlClrDemo].[MaxVariance];
GO
```

To test the custom aggregate function, we have an `Employees` table which contains the following data.

<img src="https://raw.githubusercontent.com/zzzprojects/sqlclr-tutorial/master/docs/images/clr-aggregates2.png" alt="clr aggregates-2">

Now use the `MaxVariance` aggregate in the following T-SQL.

```sql
SELECT dbo.MaxVariance(Salary) As MaxVariance
FROM Employees
```

The result of this statement will show the difference between the high and low values found in the `Salary` column of `Employees` table.

<img src="https://raw.githubusercontent.com/zzzprojects/sqlclr-tutorial/master/docs/images/clr-aggregates3.png" alt="clr aggregates-3">
