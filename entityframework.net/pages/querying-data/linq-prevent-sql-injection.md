---
PermaID: 1000080
Name: LINQ & SQL Injection
---

# LINQ Prevent SQL Injection

## Do LINQ is SQL Injection safe? 

SQL injection is a code injection technique used to attack data-driven applications, and that might destroy your database. It is one of the most common web hacking techniques placing malicious code in SQL statements, via web page input.

Now the question is will using LINQ to SQL help prevent SQL injection?

### StackOverflow Related Questions

 - [Will using LINQ to SQL help prevent SQL injection](https://stackoverflow.com/questions/473173/will-using-linq-to-sql-help-prevent-sql-injection)
 - [Does LINQ To SQL C# eliminate any possibility of a SQL injection attack?](https://stackoverflow.com/questions/6853723/does-linq-to-sql-c-sharp-eliminate-any-possibility-of-a-sql-injection-attack)
## Answer

Yes, LINQ to SQL helps to stop SQL injection, because it passes all data to the database via SQL parameters. LINQ queries are not composed by using string manipulation or concatenation, that's why they are not susceptible to traditional SQL injection attacks.