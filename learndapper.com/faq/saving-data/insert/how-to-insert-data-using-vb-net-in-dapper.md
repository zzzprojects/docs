---
id: 66ecedf3-c885-4a86-ad06-d6026fef5a33
position: 9
title: How to insert data using VB.NET in Dapper?
---

Here is an example of inserting data using VB.NET syntax:

```vbnet
Using connection As New SqlConnection(connectionString)
    Dim sql = "INSERT INTO Customers (Name, Email) VALUES (@Name, @Email)"
    
    Dim customer = New Customer() With { .Name = "Learn Dapper", .Email = "learndapper@example.com" }
    
    connection.Execute(sql, customer)
End Using
```