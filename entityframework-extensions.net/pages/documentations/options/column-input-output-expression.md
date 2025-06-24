---
Name: Column InputOutput Expression
LastMod: 2025-06-23
---

# Column InputOutput Expression

## Description

The `ColumnInputOutputExpression` allows you to choose specific properties on which you want to perform the bulk operations with the direction `InputOutput`.

The key is required for operations such as `BulkUpdate` and `BulkMerge`. The following example uses `CustomerID`, `Description`, and `IsActive` properties in the `ColumnInputOutputExpression`. 

```csharp
using (var context = new EntityContext())
{
    List<Customer> list =  new List<Customer>() { new Customer() { Name ="Customer_A", Description = "Description" , IsActive = false }, 
    new Customer()  { Name ="Customer_B", Description = "Description", IsActive = true },
    new Customer() { Name ="Customer_C", Description = "Description" , IsActive = true }};
			
    context.BulkMerge(list, options => 
        options.ColumnInputOutputExpression = c => new 
	{
	    c.Description, 
	    c.IsActive
	}
    );	
}
```

Try it: [EF Core](https://dotnetfiddle.net/wAEaSb) | [EF6](https://dotnetfiddle.net/3js97I)

- It will merge data for the `CustomerID`, `Description` and `IsActive` fields, and all other properties will remain `NULL` in the database.
- It will also update the list by updating only the specified properties from the database.
