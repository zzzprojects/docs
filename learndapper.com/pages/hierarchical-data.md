---
title: Dapper Inheritance, TPH, TPT, TPC
description: Dapper support all kind of complex inheritance patterns such as Table-per-Hierarchy (TPH), Table-per-Type (TPT), and Table-per-Concrete Class (TPC)
canonical: /hierarchical-data
status: Published
lastmod: 2023-01-05T12:52:27Z
---

# Using Inheritance in Dapper

There are several different ways to work with inheritance when using it to map out your database. You can use the **Table-per-Hierarchy (TPH)** pattern, the **Table-per-Type (TPT)** pattern, or the **Table-per-Concrete Class (TPC)** pattern. Each option has its advantages, so it's essential to understand their differences and how they can be used in your application.

### Dapper TPH Inheritance

The **Table-per-Hierarchy (TPH)** pattern is the most common approach to working with inheritance in Dapper. That involves putting all the fields for each type into a single table and then using a `discriminator` field to distinguish between the different types. 

 - The `discriminator` field can be a single column with an enumeration or multiple columns forming a unique identifier for each type. 
 - The advantage of this approach is that most queries will use the same table, and you don't need to worry about joins across multiple tables.

### Dapper TPT Inheritance

The **Table-per-Type (TPT)** pattern is similar to TPH, but instead of having all the types in a single table, each type has its table with only the fields for that particular type. 

 - This approach allows you to easily add or remove columns from specific types without affecting others. 
 - The downside is that queries often require joins between multiple tables, which can affect performance.

### Dapper TPC Inheritance

The **Table-per-Concrete Class (TPC)** pattern is the most granular approach to working with inheritance. 

 - This involves creating a separate table for each concrete class and storing all its fields in that table. 
 - The advantage of this approach is that you don't need to worry about joins, and you can easily add or remove fields without affecting other types. 
 - The downside is that it requires more tables, which increases the complexity of your database structure.

Whichever approach you choose to work with inheritance, make sure to understand their differences and how they will affect your application's performance and maintainability. Each option has its benefits and drawbacks, so it's important to consider your options carefully before making a decision. With the right approach, you can ensure that your database works efficiently and that data integrity is maintained. 

## Inheritance in Dapper

Dapper includes a feature for working with data that might map to different types from row to row. This feature is particularly useful when working with an inheritance hierarchy that uses the [Table Per Hierarchy](https://www.learnentityframeworkcore.com/inheritance/table-per-hierarchy) storage pattern, i.e. where one table is used to represent all classes in the hierarchy. In addition, a "discriminator" column is used to differentiate between types.

The following class definitions represent an inheritance hierarchy based on an abstract `Contract` type. Three derived types are defined, too, representing different types of contracts offered by a communications/media business. The `ContractType` property acts as the discriminator.

```csharp
public abstract class Contract
{
    public int ContractId { get; set; }
    public DateTime StartDate { get; set; }
    public int DurationMonths { get; set;}
    public decimal Charge { get; set; }
    public ContractType ContractType { get; set; }
    public int CustomerId { get; set; }
}
 
public class MobileContract : Contract
{
    public MobileContract() => ContractType = ContractType.Mobile;
    public string MobileNumber { get; set; }
}
 
public class TvContract : Contract
{
    public TvContract() => ContractType = ContractType.TV;
    public TVPackageType TVPackageType { get; set; }
}
 
public class BroadBandContract : Contract
{
    public BroadBandContract() => ContractType = ContractType.Broadband;
    public int DownloadSpeed { get; set; }
}
 
public enum TVPackageType
{
    S, M, L, XL
}
 
public enum ContractType
{
    Mobile = 1, TV, Broadband 
}
```

These classes are mapped to a single table with the following schema (SQLite):

![Image](/images/16-05-2019-08-47-09.png)

The following code shows how to use the `ExecuteReader` and `GetRowsParser<T>` methods to populate three collections based on the type of contract currently being read:

```csharp
var tvContracts = new List<TvContract>();
var mobileContracts = new List<MobileContract>();
var broadbandContracts = new List<BroadbandContract>();
 
var sql = @"SELECT * FROM Contracts";
 
using (var connection = new SQLiteConnection(connString))
using (var reader = connection.ExecuteReader(sql))
{
    var tvContractParser = reader.GetRowParser<TvContract>();
    var mobileContractParser = reader.GetRowParser<MobileContract>();
    var broadbandContractParser = reader.GetRowParser<BroadbandContract>();
	
    while(reader.Read())
    {
        var discriminator = (ContractType)reader.GetInt32(reader.GetOrdinal(nameof(ContractType)));
        switch(discriminator)
        {
            case ContractType.TV:
                tvContracts.Add(tvContractParser(reader));
                break;
            case ContractType.Broadband:
                broadbandContracts.Add(broadbandContractParser(reader));
                break;
            case ContractType.Mobile:
                mobileContracts.Add(mobileContractParser(reader));
                break;
        }
    }
}
 
// TV Contracts
Console.WriteLine("TV Contracts");
tvContracts.ForEach(c => Console.WriteLine($"Duration: {c.DurationMonths} months, Package Type: {c.TVPackageType.ToString()}"));
 
// Broadband Contracts
Console.WriteLine("Broadband Contracts");
broadbandContracts.ForEach(c => Console.WriteLine($"Duration: {c.DurationMonths} months, Cost: {c.Charge}, Download: {c.DownloadSpeed} Mbps"));
 
// Mobile Contracts
Console.WriteLine("Mobile Contracts");
mobileContracts.ForEach(c => Console.WriteLine($"Duration: {c.DurationMonths} months, Number: {c.MobileNumber}"));
```

![Image](/images/17-05-2019-10-35-12.png)