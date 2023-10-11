---
id: 3e636749-14e1-435b-b244-306b94bd6efa
position: 1
title: How to fix in EF Core: System.InvalidOperationException: The ConnectionString property has not been initialized.
---

<div class="h9">Exception Message:</div>

```
PM> Update-Database
Build started...
Build succeeded.
System.InvalidOperationException: The ConnectionString property has not been initialized.
   at Microsoft.Data.SqlClient.SqlConnection.PermissionDemand()
   at Microsoft.Data.SqlClient.SqlConnectionFactory.PermissionDemand(DbConnection outerConnection)
   at Microsoft.Data.ProviderBase.DbConnectionInternal.TryOpenConnectionInternal(DbConnection outerConnection, DbConnectionFactory connectionFactory, TaskCompletionSource`1 retry, DbConnectionOptions userOptions)
   at Microsoft.Data.ProviderBase.DbConnectionClosed.TryOpenConnection(DbConnection outerConnection, DbConnectionFactory connectionFactory, TaskCompletionSource`1 retry, DbConnectionOptions userOptions)
   at Microsoft.Data.SqlClient.SqlConnection.TryOpen(TaskCompletionSource`1 retry, SqlConnectionOverrides overrides)
   at Microsoft.Data.SqlClient.SqlConnection.Open(SqlConnectionOverrides overrides)
   at Microsoft.EntityFrameworkCore.SqlServer.Storage.Internal.SqlServerConnection.OpenDbConnection(Boolean errorsExpected)
   at Microsoft.EntityFrameworkCore.Storage.RelationalConnection.OpenInternal(Boolean errorsExpected)
   at Microsoft.EntityFrameworkCore.Storage.RelationalConnection.Open(Boolean errorsExpected)
   at Microsoft.EntityFrameworkCore.SqlServer.Storage.Internal.SqlServerDatabaseCreator.<>c__DisplayClass18_0.<Exists>b__0(DateTime giveUp)
   at Microsoft.EntityFrameworkCore.ExecutionStrategyExtensions.<>c__DisplayClass12_0`2.<Execute>b__0(DbContext _, TState s)
   at Microsoft.EntityFrameworkCore.SqlServer.Storage.Internal.SqlServerExecutionStrategy.Execute[TState,TResult](TState state, Func`3 operation, Func`3 verifySucceeded)
   at Microsoft.EntityFrameworkCore.ExecutionStrategyExtensions.Execute[TState,TResult](IExecutionStrategy strategy, TState state, Func`2 operation, Func`2 verifySucceeded)
   at Microsoft.EntityFrameworkCore.SqlServer.Storage.Internal.SqlServerDatabaseCreator.Exists(Boolean retryOnNotExists)
   at Microsoft.EntityFrameworkCore.SqlServer.Storage.Internal.SqlServerDatabaseCreator.Exists()
   at Microsoft.EntityFrameworkCore.Migrations.HistoryRepository.Exists()
   at Microsoft.EntityFrameworkCore.Migrations.Internal.Migrator.Migrate(String targetMigration)
   at Microsoft.EntityFrameworkCore.Design.Internal.MigrationsOperations.UpdateDatabase(String targetMigration, String connectionString, String contextType)
   at Microsoft.EntityFrameworkCore.Design.OperationExecutor.UpdateDatabaseImpl(String targetMigration, String connectionString, String contextType)
   at Microsoft.EntityFrameworkCore.Design.OperationExecutor.UpdateDatabase.<>c__DisplayClass0_0.<.ctor>b__0()
   at Microsoft.EntityFrameworkCore.Design.OperationExecutor.OperationBase.Execute(Action action)
The ConnectionString property has not been initialized.
```

<div class="image-outer"><img src="https://www.learnentityframeworkcore.com/images/efcore/migrations/update-database/troubleshooting-the-connectionstring-property-has-not-been-initialized.png" loading="lazy" alt="Exception - The ConnectionString property has not been initialized"></div>

<div class="h9">Cause:</div>

This error arises when EF Core fails to locate the connection string in your configuration file, resulting in an empty connection string being used.

In the example provided, the `Configuration` property is initialized during application startup. However, EF Core doesn't set it when using the `IDesignTimeDbContextFactory`.


```csharp
namespace Z
{
    public class MyContextNameFactory : IDesignTimeDbContextFactory<Z.MyContextName>
    {
        public static IConfigurationRoot Configuration { get; set; }
        public Z.MyContextName CreateDbContext(string[] args)
        {
            // return an empty string as the `Configuration` property has not been correctly initialized.
            var connectionString = Configuration.GetConnectionString("DefaultConnection");

            var optionsBuilder = new DbContextOptionsBuilder<Z.MyContextName>();
            optionsBuilder.UseSqlServer(new SqlConnection(connectionString));

            return new Z.MyContextName(optionsBuilder.Options);
        }
    }
}

```

<div class="h9">Solution:</div>

To fix this, ensure the `Configuration` property is initialized the same way you typically do in your project:

```csharp
namespace Z
{
    public class MyContextNameFactory : IDesignTimeDbContextFactory<Z.MyContextName>
    {
        public static IConfigurationRoot Configuration { get; set; }

        static MyContextNameFactory() 
        {
            var builder = new ConfigurationBuilder()
                .SetBasePath(Directory.GetCurrentDirectory())
                .AddJsonFile("appsettings.json", optional: true, reloadOnChange: true);

            IConfigurationRoot configuration = builder.Build();
            MyContextNameFactory.Configuration = configuration;
        }

        public Z.MyContextName CreateDbContext(string[] args)
        {
            var connectionString = Configuration.GetConnectionString("DefaultConnection");

            var optionsBuilder = new DbContextOptionsBuilder<Z.MyContextName>();
            optionsBuilder.UseSqlServer(new SqlConnection(connectionString));

            return new Z.MyContextName(optionsBuilder.Options);
        }
    }
}
```

Some other solution might be required depending on the scenario:

- Check if `appsettings.json` is copied to the output directory.
- Verify the `name` of the connection string matches the one in `appsettings.json`.