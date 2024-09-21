---
Title: 100+ Options to Improve Flexibility in Your Data Saving Operations 
MetaDescription: 100+ Options to Improve Flexibility in Your Data Saving Operations 
LastMod: 2024-09-21
---

# 100+ Options to Add Flexibility to Your Data Saving Operations

Being able to save your entities quickly is crucial, but having the flexibility to save them **THE WAY YOU WANT** often holds greater importance, especially when using [Bulk Extensions Methods](/bulk-extensions-methods) and [Single Extensions Methods](/single-extensions-methods).

Before we learn into all the available options, let's first understand how to use them with the `UseBulkOptions` method: 

- From Mapping
- From Connection and Transaction

## From Mapping

You can utilize the `UseBulkOptions` method by incorporating it into the mapping for a specific entity type. This ensures that every time your mapping is invoked, the bulk operations will employ these pre-defined options:

```csharp
DapperPlusManager.Entity<Product>()
	.Table("Product")
	.Identity(x => x.ProductID)
	.UseBulkOptions(x => x.InsertIfNotExists = true);
	
connection.BulkInsert(products);
```

You can also use a [mapping key](/mapping-key) if the [mapping](/mapping) is not the default one used for saving your entities.

[Online Example](https://dotnetfiddle.net/1Hp8kC)

## From Connection and Transaction

You can call the `UseBulkOptions` method directly from the connection or transaction before chaining your bulk operations methods. When configured in this manner, all subsequent bulk operations will use the specified options. Note that only general options that are not related to a specific entity type, such as `Audit`, `BatchSize`, or `Log`, are available in this context:

```csharp
connection.UseBulkOptions(options => options.Log = s => Console.WriteLine(s))
		  .BulkInsert(products);
```

[Online Example](https://dotnetfiddle.net/TUu0bl)

## Options

This section is currently under development as of 2024-09-21 and will soon be enhanced. Our upcoming updates will include better categorization of options, along with detailed descriptions and practical examples. Stay tuned for these improvements, designed to enhance your understanding and usage of the available settings in our library.

- Mapping
   - CaseSensitive
   - ColumnInputExpression
   - ColumnInputNames
   - ColumnInputOutputExpression
   - ColumnInputOutputNames
   - ColumnMappings
   - ColumnOutputExpression
   - ColumnOutputNames
   - ColumnPrimaryKeyExpression
   - ColumnPrimaryKeyNames
   - ColumnStagingTableFormulaExpression
   - ColumnStagingTableFormulaNames
   - ForceOutputFromUnmodifiedRow
   - IgnoreColumnOutputExpression
   - IgnoreColumnOutputNames
   - AutoTruncate
   - DataSource
   - DestinationSchemaName
   - DestinationTableName
   - AllowDuplicateKeys
   - AllowUpdatePrimaryKeys
   - UseSmartMatchNames
   - UseTableLock
   - ValidateAllDestinationMapped
   - ValidateAllSourceMapped
   - ValidateNoDuplicateKey
   - ForceRowsAffectedValidation
   - DisableDotCheckForEscapeTableName
   - IsReadOnly
   - MatchNamesWithUnderscores
- Audit
   - AuditEntries
   - AuditMode
   - UseAudit
- AutoMap
   - AutoMap
   - AutoMapColumnBuilder
   - AutoMapIdentityExpression
   - AutoMapIdentityName
   - AutoMapKeyExpression
   - AutoMapKeyName
   - AutoMapOutputDirection
   - AutoMapOutputIdentity
   - AutoMapValueFactory
- Batch
   - BatchDelayInterval
   - BatchSize
   - BatchTimeout
- Danger Zone
   - UnsafeMode
- Log
   - Log
   - UseLogDump
   - LogDump
   - AddLogExecuting
   - StopwatchForSqlExecutingTime
   - UseStopwatchForSqlExecutingTime
- Temporary Table
   - TemporaryTableBatchByTable
   - TemporaryTableCreate
   - TemporaryTableInsertBatchSize
   - TemporaryTableIsMemory
   - TemporaryTableMinRecord
   - TemporaryTableName
   - TemporaryTablePersist
   - TemporaryTableSchemaName
   - TemporaryTableUseSameName
   - TemporaryTableUseTableLock
- Parallel
   - ParallelBatchByTask
   - ParallelMinBatch
   - ParallelMinRecord
   - UseParallel
- Result Info
   - UseRowsAffected 
   - ResultInfo
   - RowsAffected
- Customization
   - ForceSelectOutput
   - CommandParameterAddRowIndex
   - CommandParameterPrefix
   - CommandParameterSuffix
   - CombineMaxCommand
   - UsePermanentTable
   - Resolution
   - RetryCount
   - RetryInterval
   - ErrorMode
   - Errors
   - DefaultValueResolution
   - DisableInformationSchemaCache
- Connection & Providers
   - Connection
   - ConnectionFactory
   - ConnectionName
   - ConnectionString
   - CustomProvider
   - Transaction
   - TransactionIsolationLevel
   - UseInternalTransaction
   - InternalTransaction
   - Provider
   - ProviderAssembly
- Optimization
   - QueryHint
   - TableHintSql
   - DisableTemporaryTableClusteredIndex
   - DisablePrimaryKeyNullCheck
- Event
   - BulkOperationExecuted
   - BulkOperationExecuting
- Insert
   - InsertIfNotExists
   - InsertKeepIdentity
   - InsertNotMatchedAndFormula
   - InsertPrimaryKeyAndFormula
   - InsertStagingTableFilterFormula
   - IgnoreOnInsertExpression
   - IgnoreOnInsertNames
- Update
   - CoalesceOnUpdateExpression
   - CoalesceOnUpdateNames
   - CoalesceDestinationOnUpdateExpression
   - CoalesceDestinationOnUpdateNames
   - IgnoreOnUpdateExpression
   - IgnoreOnUpdateMatchedAndConditionExpression
   - IgnoreOnUpdateMatchedAndConditionNames
   - IgnoreOnUpdateMatchedAndOneNotConditionExpression
   - IgnoreOnUpdateMatchedAndOneNotConditionNames
   - IgnoreOnUpdateNames
   - OnUpdateUseCoalesce
   - OnUpdateUseCoalesceDestination
   - UpdateMatchedAndConditionExpression
   - UpdateMatchedAndConditionNames
   - UpdateMatchedAndFormula
   - UpdateMatchedAndOneNotConditionExpression
   - UpdateMatchedAndOneNotConditionNames
   - UpdatePrimaryKeyAndFormula
   - UpdateStagingTableFilterFormula
- Delete
   - DeleteMatchedAndConditionExpression
   - DeleteMatchedAndConditionNames
   - DeleteMatchedAndFormula
   - DeleteMatchedAndOneNotConditionExpression
   - DeleteMatchedAndOneNotConditionNames
   - DeletePrimaryKeyAndFormula
   - DeleteStagingTableFilterFormula
   - IgnoreOnDeleteMatchedAndConditionExpression
   - IgnoreOnDeleteMatchedAndConditionNames
   - IgnoreOnDeleteMatchedAndOneNotConditionExpression
   - IgnoreOnDeleteMatchedAndOneNotConditionNames
- Merge
   - CoalesceOnMergeUpdateExpression
   - CoalesceOnMergeUpdateNames
   - CoalesceDestinationOnMergeUpdateExpression
   - CoalesceDestinationOnMergeUpdateNames
   - IgnoreOnMergeInsert
   - IgnoreOnMergeInsertExpression
   - IgnoreOnMergeInsertNames
   - IgnoreOnMergeMatchedAndConditionExpression
   - IgnoreOnMergeMatchedAndConditionNames
   - IgnoreOnMergeMatchedAndOneNotConditionExpression
   - IgnoreOnMergeMatchedAndOneNotConditionNames
   - IgnoreOnMergeUpdate
   - IgnoreOnMergeUpdateExpression
   - IgnoreOnMergeUpdateNames
   - MergeKeepIdentity
   - MergeMatchedAndConditionExpression
   - MergeMatchedAndConditionNames
   - MergeMatchedAndFormula
   - MergeMatchedAndOneNotConditionExpression
   - MergeMatchedAndOneNotConditionNames
   - MergeNotMatchedAndFormula
   - MergePrimaryKeyAndFormula
   - MergeStagingTableFilterFormula
   - OnMergeInsertInputExpression
   - OnMergeInsertInputNames
   - OnMergeUpdateInputExpression
   - OnMergeUpdateInputNames
   - OnMergeUpdateUseCoalesce
   - OnMergeUpdateUseCoalesceDestination
- Synchronize
   - CoalesceOnSynchronizeUpdateExpression
   - CoalesceOnSynchronizeUpdateNames
   - CoalesceDestinationOnSynchronizeUpdateExpression
   - CoalesceDestinationOnSynchronizeUpdateNames
   - ColumnSynchronizeDeleteKeySubsetExpression
   - ColumnSynchronizeDeleteKeySubsetFormula
   - ColumnSynchronizeDeleteKeySubsetNames
   - IgnoreOnSynchronizeInsertExpression
   - IgnoreOnSynchronizeInsertNames
   - IgnoreOnSynchronizeMatchedAndConditionExpression
   - IgnoreOnSynchronizeMatchedAndConditionNames
   - IgnoreOnSynchronizeMatchedAndOneNotConditionExpression
   - IgnoreOnSynchronizeMatchedAndOneNotConditionNames
   - IgnoreOnSynchronizeUpdateExpression
   - IgnoreOnSynchronizeUpdateNames
   - OnSynchronizeInsertInputExpression
   - OnSynchronizeInsertInputNames
   - OnSynchronizeUpdateInputExpression
   - OnSynchronizeUpdateInputNames
   - OnSynchronizeUpdateUseCoalesce
   - OnSynchronizeUpdateUseCoalesceDestination
   - SynchronizeDeleteDestinationTableFilterFormula
   - SynchronizeIgnoreInsert
   - SynchronizeIgnoreUpdate
   - SynchronizeKeepidentity
   - SynchronizeMatchedAndConditionExpression
   - SynchronizeMatchedAndConditionNames
   - SynchronizeMatchedAndFormula
   - SynchronizeMatchedAndOneNotConditionExpression
   - SynchronizeMatchedAndOneNotConditionNames
   - SynchronizeNotMatchedAndFormula
   - SynchronizeOnlyDelete
   - SynchronizePrimaryKeyAndFormulaDelete
   - SynchronizePrimaryKeyAndFormulaMerge
   - SynchronizeSoftDeleteFormula
Lambda
   - LambdaCoalesceDestinationOnMergeUpdateExpression
   - LambdaCoalesceDestinationOnSynchronizeUpdateExpression
   - LambdaCoalesceDestinationOnUpdateExpression
   - LambdaCoalesceOnMergeUpdateExpression
   - LambdaCoalesceOnSynchronizeUpdateExpression
   - LambdaCoalesceOnUpdateExpression
   - LambdaColumnStagingTableFormulaExpression
   - LambdaDeleteMatchedAndConditionExpression
   - LambdaDeleteMatchedAndOneNotConditionExpression
   - LambdaIgnoreColumnOutputExpression
   - LambdaIgnoreOnDeleteMatchedAndConditionExpression
   - LambdaIgnoreOnDeleteMatchedAndOneNotConditionExpression
   - LambdaIgnoreOnInsertExpression
   - LambdaIgnoreOnMergeInsertExpression
   - LambdaIgnoreOnMergeMatchedAndConditionExpression
   - LambdaIgnoreOnMergeMatchedAndOneNotConditionExpression
   - LambdaIgnoreOnMergeUpdateExpression
   - LambdaIgnoreOnSynchronizeInsertExpression
   - LambdaIgnoreOnSynchronizeMatchedAndConditionExpression
   - LambdaIgnoreOnSynchronizeMatchedAndOneNotConditionExpression
   - LambdaIgnoreOnSynchronizeUpdateExpression
   - LambdaIgnoreOnUpdateExpression
   - LambdaInputExpression
   - LambdaInputOutputExpression
   - LambdaMergeMatchedAndConditionExpression
   - LambdaMergeMatchedAndOneNotConditionExpression
   - LambdaOnMergeInsertInputExpression
   - LambdaOnMergeUpdateInputExpression
   - LambdaOnSynchronizeInsertInputExpression
   - LambdaOnSynchronizeUpdateInputExpression
   - LambdaOutputExpression
   - LambdaPrimaryKeyExpression
   - LambdaSynchronizeDeleteKeySubsetExpression
   - LambdaSynchronizeMatchedAndConditionExpression
   - LambdaSynchronizeMatchedAndOneNotConditionExpression
   - LambdaUpdateMatchedAndConditionExpression
   - LambdaUpdateMatchedAndOneNotConditionExpression
- PostgreSQL
   - UsePostgreSqlGetInformationSchema2
   - UsePostgreSqlInsertOnConflictDoNothing
   - UsePostgreSqlInsertOverridingSystemValue
   - UsePostgreSqlInsertOverridingUserValue
   - UsePostgreSqlTimeZone
   - UsePostgreSqlUnprepare
   - PostgreSqlBinaryImportTimeout
- Oracle
   - UseOracleGlobalSequenceIndexTable 
- SQL Server
   - SqlBulkCopyOptions
   - DisableSqlBulkCopyDirect
   - UseTempDbForColumnEncrypted
   - ForceTriggerResolution
   - HasColumnEncrypted
- Internal
   - Debug1
   - IncludeGraph
   - IncludeGraphOperationBuilder
   - UseCompile
   - TestExecuteAsync
   - UseLegacyBatchOperation
   - IsCheckConstraintOnInsertDisabled
   - DeleteFromQueryExecuted

## Conclusion

In this article, we've explored the extensive range of options Dapper Plus offers. It's understandable to feel a bit overwhelmed by the sheer number of choices available. Typically, a developer might use about 10 options throughout a project. However, if you have specific requirements, it's likely that one of our options can meet your needs. Should you need further clarification or have unique scenarios, you might want to revisit this page or [contact us directly](/contact-us) for assistance.