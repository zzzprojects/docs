---
PermaID: 1000078
Name: Substract Two Dates
---

# Subtract Two Dates

## How to subtract two dates? 

How to determine the number of days between 2 dates using LINQ with Entity Framework? Let's take a look at a very simple example where we want to retrieve all those orders which are placed in the last 100 days.


```csharp
using (var ctx = new MyContext())
{
    int noOfDays = 30;
    var orders = context.Invoices
        .Where(i => DateTime.Now.Subtract(s.Date).TotalDays > noOfDays)
        .ToList();
}
```

when you execute the above code, then it will throw the following exception.

`System.NotSupportedException: LINQ to Entities does not recognize the method 'System.TimeSpan Subtract(System.DateTime)' method, and this method cannot be translated into a store expression.`

### StackOverflow Related Questions

 - [LINQ to Entities for subtracting 2 dates](https://stackoverflow.com/questions/570858/linq-to-entities-for-subtracting-2-dates)

## Answer

The easiet way to subtract two dates using LINQ is to define a `DateTime` variable that represents the old date, then use that `DateTime` variable in where portion of the LINQ query.


```csharp
using (var context = new EntityContext())
{	
	int noOfDays = 30;
	DateTime oldDate = DateTime.Now.Subtract(new TimeSpan(noOfDays, 0, 0, 0, 0));

	var invoices = context.Invoices
 		.Where(i => i.Date > oldDate)
 		.ToList();
}
```
[Try it online](https://dotnetfiddle.net/C0vwtL)