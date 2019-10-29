---
PermaID: 1000101
Name: Verison History
---

# Version History

The first two versions of Entity Framework shipped with the .NET Framework and had versions numbers that aligned with the version of the framework that they were included in (3.5 and 4). Later, this EF started shipping independently through [NuGet](https://www.nuget.org)

## EF6

### [Entity Framework 6.2.0](https://www.nuget.org/packages/EntityFramework/6.2.0)

***Release Date: Oct 26, 2017***

#### Features/Changes

 - Reduce startup time by loading finished code first models from a persistent cache
 - Fluent API to define indexes
 - DbFunctions.Like() to enable writing LINQ queries that translate to LIKE in SQL
 - Migrate.exe should support -script option 
 - EF6 does not work with primary key from sequence 
 - Update error numbers for SQL Azure Execution Strategy 
 - Bug: Retrying queries or SQL commands fails with "The SqlParameter is already contained by another SqlParameterCollection." 
 - Bug: Evaluation of DbQuery.ToString() frequently times out in the debugger.

### [Entity Framework 6.1.3](https://www.nuget.org/packages/EntityFramework/6.1.3)

***Release Date: Mar 10, 2015***

#### Features/Changes

 - Query: Regression in EF 6.1.2: OUTER APPLY introduced and more complex queries for 1:1 relationships and "let" clause 
 - TPT problem with hiding base class property in inherited class 
 - DbMigration.Sql fails when the word 'go' is contained in the text 
 - Create compatibility flag for UnionAll and Intersect flattening support 
 - Query with multiple Includes does not work in 6.1.2 (working in 6.1.1) 
 - "You have an error in your SQL syntax" after upgrading from EF 6.1.1 to 6.1.2

### [Entity Framework 6.1.2](https://www.nuget.org/packages/EntityFramework/6.1.2)

***Release Date: Jan 22, 2015***

#### Features/Changes

EF6.1.2 is mostly about bug fixes; We also accepted a couple of noteworthy changes from members of the community: 
 
- Query cache parameters can be configured from the app/web.configuration file


```csharp     <entityFramework> 
        <queryCache size='1000' cleaningIntervalInSeconds='-1'/> 
     </entityFramework>
```
- SqlFile and SqlResource methods on DbMigration allow you to run a SQL script stored as a file or embedded resource. 

### [Entity Framework 6.1.1](https://www.nuget.org/packages/EntityFramework/6.1.1)

***Release Date: Oct 22, 2014***

#### Features/Changes

 - Designer: Error opening EF5 edmx with decimal precision in EF6 designer 
 - Default instance detection logic for LocalDB doesn't work with SQL Server 2014 

### [Entity Framework 6.1.0](https://www.nuget.org/packages/EntityFramework/6.1.0)

***Release Date: Apr 30, 2014***

#### Features/Changes

 - **Tooling** consolidation provides a consistent way to create a new EF model. This feature extends the ADO.NET Entity Data Model wizard to support creating Code First models, including reverse engineering from an existing database. These features were previously available in Beta quality in the EF Power Tools. 
 - **Handling of transaction commit failures** provides the CommitFailureHandler which makes use of the newly introduced ability to intercept transaction operations. The CommitFailureHandler allows automatic recovery from connection failures while committing a transaction. 
 - **IndexAttribute** allows indexes to be specified by placing an [Index] attribute on a property (or properties) in your Code First model. Code First will then create a corresponding index in the database. 
 - **The public mapping API** provides access to the information EF has on how properties and types are mapped to columns and tables in the database. In past releases this API was internal. 
 - **Ability to configure interceptors via the App/Web.config file** allows interceptors to be added without recompiling the application. 
 - **System.Data.Entity.Infrastructure.Interception.DatabaseLogger** is a new interceptor that makes it easy to log all database operations to a file. In combination with the previous feature, this allows you to easily switch on logging of database operations for a deployed application, without the need to recompile. 
 - **Migrations model change detection** has been improved, so that scaffolded migrations are more accurate; performance of the change detection process has also been enhanced. 
 - **Performance improvements** including reduced database operations during initialization, optimizations for null equality comparison in LINQ queries, faster view generation (model creation) in more scenarios, and more efficient materialization of tracked entities with multiple associations. 
 
### [Entity Framework 6.0.2](https://www.nuget.org/packages/EntityFramework/6.0.2)

***Release Date: Apr 30, 2014***

#### Features/Changes

 - The 6.0.2 patch release is limited to fixing issues that were introduced in the EF6 release (regressions in performance/behavior since EF5). 

### [Entity Framework 6.0.1](https://www.nuget.org/packages/EntityFramework/6.0.1)

***Release Date: Oct 28, 2013***

#### Features/Changes

 - The most notable changes were to fix some performance issues during the warm-up for EF models. 
 - This was important as the warm-up performance was an area of focus in EF6 and these issues were negating some of the performance gains made in EF6. 

### [Entity Framework 6.0.0](https://www.nuget.org/packages/EntityFramework/6.0.0)

***Release Date: Oct 17, 2013***

#### Features/Changes

 - **Async Query and Save** add support for the task-based asynchronous patterns that were introduced in .NET 4.5. 
 - **Connection Resiliency** enables automatic recovery from transient connection failures. 
 - **Code-Based Configuration** gives you the option of performing configuration - that was traditionally performed in a config file - in code. 
 - **Dependency Resolution** introduces support for the Service Locator pattern and we've factored out some pieces of functionality that can be replaced with custom implementations. 
 - **Interception/SQL logging** provides low-level building blocks for interception of EF operations with simple SQL logging built on top. 
 - **Testability improvements** make it easier to create test doubles for DbContext and DbSet when using a mocking framework or to write your own test doubles. 
 - **DbContext can now be created with a DbConnection that is already opened** which enables scenarios where it would be helpful if the connection could be open when creating the context (such as sharing a connection between components where you can not guarantee the state of the connection). 
 - **Improved Transaction Support** provides support for a transaction external to the framework as well as improved ways of creating a transaction within the Framework. 
 - **Enums, Spatial and Better Performance on .NET 4.0** - By moving the core components that used to be in the .NET Framework into the EF NuGet package we are now able to offer enum support, spatial data types and the performance improvements from EF5 on .NET 4.0. 
 - **Improved performance of Enumerable.Contains in LINQ queries.**
 - **Improved warm up time (view generation)**, especially for large models. 
 - **Pluggable Pluralization & Singularization Service.** 
 - **Custom implementations of Equals or GetHashCode** on entity classes are now supported. 
 - **DbSet.AddRange/RemoveRange** provides an optimized way to add or remove multiple entities from a set. 
 - **DbChangeTracker.HasChanges** provides an easy and efficient way to see if there are any pending changes to be saved to the database. 
 - **SqlCeFunctions** provides a SQL Compact equivalent to the SqlFunctions. 
 - **Custom Code First Conventions** allow writing your own conventions to help avoid repetitive configuration. We provide a simple API for lightweight conventions as well as some more complex building blocks to allow you to author more complicated conventions. 
 - **Code First Mapping to Insert/Update/Delete Stored Procedures** is now supported. 
 - **Idempotent migrations scripts** allow you to generate a SQL script that can upgrade a database at any version up to the latest version. 
 - **Configurable Migrations History Table** allows you to customize the definition of the migrations history table. This is particularly useful for database providers that require the appropriate data types etc. to be specified for the Migrations History table to work correctly. 
 - **Multiple Contexts per Database** removes the previous limitation of one Code First model per database when using Migrations or when Code First automatically created the database for you. 
 - **DbModelBuilder.HasDefaultSchema** is a new Code First API that allows the default database schema for a Code First model to be configured in one place. Previously the Code First default schema was hard-coded to "dbo" and the only way to configure the schema to which a table belonged was via the ToTable API. 
 - **DbModelBuilder.Configurations.AddFromAssembly method** allows you to easily add all configuration classes defined in an assembly when you are using configuration classes with the Code First Fluent API. 
 - **Custom Migrations Operations** enabled you to add additional operations to be used in your code-based migrations. 
 - **Default transaction isolation level is changed to READ_COMMITTED_SNAPSHOT** for databases created using Code First, allowing for more scalability and fewer deadlocks. 
 - **Entity and complex types can now be nested inside classes.**

## EF5

### [Entity Framework 5.0.0](https://www.nuget.org/packages/EntityFramework/5.0.0)  

***Release Date: Nov 08, 2012***

#### Features/Changes

This release introduces some new features including;
 
 - Enum support
 - Table-valued functions
 - Spatial data types and various performance improvements.

The Entity Framework Designer in Visual Studio 2012 also introduces support for; 

 - Multiple-diagrams per model
 - Coloring of shapes on the design surface and 
 - Batch import of stored procedures.

## EF4

### [Entity Framework 4.3.1](https://www.nuget.org/packages/EntityFramework/4.3.1)

***Release Date: Feb 29, 2012***

#### Features/Changes

 - This patch release included some bug fixes to the EF 4.3 release and introduced better LocalDb support for folks using EF 4.3 with Visual Studio 2012.

### [Entity Framework 4.3.0](https://www.nuget.org/packages/EntityFramework/4.3.0)

***Release Date: Feb 09, 2012***

#### Features/Changes

 - The EF 4.3 release included the new Code First Migrations feature that allows a database created by Code First to be incrementally changed as your Code First model evolves.

### [Entity Framework 4.2.0](https://www.nuget.org/packages/EntityFramework/4.2.0)

***Release Date: Nov 01, 2011***

#### Features/Changes

 - This release includes bug fixes to the EF 4.1.1 release. 
 - Because this release only included bug fixes, it could have been the EF 4.1.2 patch release, but we opted to move to 4.2 to allow us to move away from the date based patch version numbers we used in the 4.1.x releases and adopt the [http://semver.org](http://semver.org) standard for semantic versioning.

### [Entity Framework 4.1.1](https://www.nuget.org/packages/EntityFramework/4.1.10715) 

***Release Date: Jul 25, 2011***

#### Features/Changes

 - In addition to bug fixes, this patch release introduced some components to make it easier for design time tooling to work with a Code First model. 
 - These components are used by Code First Migrations (included in EF 4.3) and the EF Power Tools. 
 - The NuGet package for this release has the version number 4.1.10715. 

### [Entity Framework 4.1](https://www.nuget.org/packages/EntityFramework/4.1.10331) 

***Release Date: Apr 12, 2011***

#### Features/Changes

 - The EF 4.1 release was the first to be published on NuGet. 
 - This release included the simplified DbContext API and the Code First workflow. 
 - Note the NuGet package for this release has the version number 4.1.10331.

### [Entity Framework 4](https://www.nuget.org/packages/EntityFramework/4.1.10311)

***Release Date: Apr 12, 2011***

#### Features/Changes

 - This release was included in [.NET Framework 4](https://www.microsoft.com/en-US/Download/confirmation.aspx?id=17718) and Visual Studio 2010. New features in this release included POCO support, lazy loading, testability improvements, customizable code generation and the Model First workflow. 
 - Although it was the second release of Entity Framework, it was named EF 4 to align with the .NET Framework version that it shipped with. After this release, we started making Entity Framework available on NuGet and adopted semantic versioning since we were no longer tied to the .NET Framework Version.

### EF or EF3.5

***Release Date: Aug 11, 2008***

#### Features/Changes

The initial release of Entity Framework was included in [.NET 3.5 SP1](https://www.microsoft.com/en-pk/download/details.aspx?id=21) and Visual Studio 2008 SP1. This release provided basic O/RM support using the Database First workflow.