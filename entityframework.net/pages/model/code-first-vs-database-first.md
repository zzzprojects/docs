---
PermaID: 1000107
Name: Code First vs Database First
---

# Code First vs Database First

## Code First vs Database First 

When you start learning Entity Framework, you will often see the the following two terms;

 - Code Fist 
 - Database First

What is the difference between **Code First** and **Database First** in Entity Framework?

### StackOverflow Related Questions

 - [What is the difference between 'Database First' and 'Code First to Existing Database' in Entity Framework](https://stackoverflow.com/questions/22695199/what-is-the-difference-between-database-first-and-code-first-to-existing-data)
 - [Code-first vs Model/Database-first [closed]](https://stackoverflow.com/questions/5446316/code-first-vs-model-database-first)

## Answer

### Code First Approach

 - Code First is a very popular approach and has full control over the code rather than database activity. 
 - In this approach, we can do all the database operations from the code and manual changes to database have been lost and everything is depending on the code.
 - In this you need to create POCO entities as data model.

[more...](/ef-code-first)

### Database First Approach

 - Database first approach is used when database is ready then Entity Framework will complete his duty and create POCO entities for you.
 - If you have already a designed database and you don't want to do extra effort then you can go with this approach.
 - You can modify the database manually and update model from database. 
 - So, we can say, entity framework is able to create your model classes based on tables and columns from relational database.

[more...](/ef-database-first)