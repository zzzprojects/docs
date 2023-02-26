---
PermaID: 1000130
Title: Entity Framework Change Connection String - How to Change at Runtime 
MetaDescription: Unlock the power of Entity Framework by understanding how to modify your connection string at runtime. Learn how you can use the context constructor overload to specify a connection string.
LastMod: 2023-02-25
Tags: getting-started connection
---

# Entity Framework Change Connection String: How to Change at Runtime

## How to change the connection at runtime? 

When multiple databases exist and you need to perform database operations on different databases based on your business logic. 

### StackOverflow Related Questions

 - [Entity Framework change connection at runtime](https://stackoverflow.com/questions/20216147/entity-framework-change-connection-at-runtime)
 - [using connection string at runtime](https://stackoverflow.com/questions/27572279/using-connection-string-at-runtime?noredirect=1&lq=1)

## Answer

***DbContext** has a constructor overload that accepts the name of a connection string or a connection string itself. Implement your own version and pass it to the base constructor.


```csharp
public CustomerContext( string connectionString) 
        : base(connectionString)
{
}
```

Then simply pass the name of a configured connection string or a connection string itself when you instantiate your DbContext.


```csharp
using (var context = new CustomerContext("connection string 1"))
{
    //code here
}

using (var context = new CustomerContext("connection string 2"))
{
    //code here
}
```