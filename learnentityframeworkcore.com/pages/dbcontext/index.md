---
title: The Entity Framework Core DbContext
description: The Entity Framework Core DbContext class's role and capabilities 
canonical: /dbcontext
status: Published
lastmod: 2025-07-11
---

# EF Core DbContext

The EF Core `DbContext` class represents a session with a database and provides an API for communicating with the database through the following capabilities:

- Database Connections
- Data operations such as querying and persistence
- Change Tracking
- Model building
- Data Mapping
- Object caching
- Transaction management

## Database Connections

The DbContext is responsible for opening and managing connections to the database. 

## Data Operations

The DbContext provides methods for performing the following data operations directly.

- [Adding data](/dbcontext/adding-data)
- [Modifying data](/dbcontext/modifying-data)
- [Deleting data](/dbcontext/deleting-data)

The `DbContext` also provides [data querying](/dbset/querying-data) capability via the DbSet property.

  

[//]: # (https://msdn.microsoft.com/en-us/magazine/mt767693.aspx)

## Change Tracking
The [Change Tracker](/dbcontext/change-tracker) detects changes made to entities and sets the `EntityState` of an object accordingly. The state of the entity determines the type of operation that the database will be asked to perform upon it, and therefore the SQL that will be generated.

## Model Building
The `DbContext` builds a [conceptual model](/model) based on [convention](/conventions) and [configuration](/configuration) and maps that to the database. The model and its mappings are built at application startup and are persisted in memory for the lifetime of the application.

## Data Mapping
The `DbContext` includes a [data mapper layer](http://martinfowler.com/eaaCatalog/dataMapper.html) responsible for mapping the results of SQL queries to entity instances and other types defined by the client application. 

## Object Caching
The DbContext provides a first-level cache for objects that it is asked to retrieve from the data store. Subsequent requests for the same object will return the cached object instead of executing another database request.

## Transaction Management
When the DbContext `SaveChanges` method is called, a transaction is created and all pending changes are wrapped in it as a single [unit of work](http://martinfowler.com/eaaCatalog/unitOfWork.html). If an error occurs when the changes are applied to the database, they are rolled back and the database is left in an unmodified condition. 

[//]: # (https://msdn.microsoft.com/en-us/library/dn456843.aspx)






