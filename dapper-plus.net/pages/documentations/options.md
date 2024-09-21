---
Title: 100+ Options to Improve Flexibility in Your Data Saving Operations 
MetaDescription: 100+ Options to Improve Flexibility in Your Data Saving Operations 
LastMod: 2024-09-21
---

# 100+ Options to Improve Flexibility in Your Data Saving Operations

// TODO: We can also give option for a column... MapWithOptions

Being able to save entity very fast is always important but being able to save entity the **WAY YOU WANT** is often more important when using [Bulk Methods](#) and [Single Methods](#).

We just saw in the previous article how to [map your entity](#) for common scenario. However, this mapping only cover a subset of all option our library support.

Before learning more about all the [options](#), let learn first how to use the method `UseBulkOptions`:

- [From Mapping](#)
- [From Connection and Transaction](#)

## From Mapping

You can use the `UseBulkOptions` method by adding it to your mapping for a specific entity type. So every time your mapping is called, the bulk operations under the hood will use those options:

```csharp
todo
```

For options that are specific for an entity type or should always be used when saved this entity type, this is the recommanded way.

[Mapping Key]

## From Connection and Transaction

You can use the `UseBulkOptions` method by chaining it as the first method to a connection or transaction. When performing this way, all bulk operations chained after will use those options.

```csharp
```

This is the recommand way for option that are more generic such as auditing and logging. Options that are generic for a type should not be used this way.

As their will be used for maybe more than 1 type, you should only specify global option here like a `BatchSize` or if you want to use the `Audit`.

You can specify options that are more generate during the connection such as `Audit`, `BatchSize`, and `Logging`.
```csharp
todo
```

## Options

This section is under developement since 2024-09-21, it will soon be improved.

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

In this article, we only have seen an introduction to all options we provide. Fortunately (because this is a good thing), our library provide too many options to cover them all in a getting started.

We highly encourage you to read from time to time our [Documention - Options & UseBulkOptions](#), to better understand everything you can do through our library.

One major problem when providing options through the mapping is you might need different options for different scenario, so how can you handle this? Obviously that is not a problem as the next article about [Mapping Key](#) will solve this.