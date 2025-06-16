---
Name: Logging
---

# Logging

## Description

The Bulk Operations `Logging` feature lets you log all messages of type "Information".


## Key Features

- Allows you to see SQL statements that are executed
- Allows you to see parameters and execution time

## Getting Started

There are two ways to use the logging features

### Logging with an action

To use the `Logging` feature with an action, you need to define a delegate to execute.

```csharp
TBD
```

### Logging with the LogDump

To use the `Logging` feature with the LogDump, you need to use the `UseBulkOptions` method to set:
- `UseLogDump = true`: To enable the `LogDump`.
- `LogDump = sb`: To specify the `StringBuilder` to use to dump all messages.

```csharp
// Execute
var sb = new StringBuilder();
using (var bulk = new BulkOperation(connection))
{
    bulk.DestinationTableName = "Customer";
    bulk.UseLogDump = true;
    
    bulk.BulkMerge(list);

    sb = bulk.LogDump;
}

// Result
Console.WriteLine(sb.ToString());
```

## Scenarios

- Log into a Database
- Log into a File
- Log into NLog

## Options

| Name                               | Description                                                           |
|:-----------------------------------|:----------------------------------------------------------------------|
|[Log](log.md)  | The `Log` property is an action executed when a message of type "Information" happens. |
|[UseLogDump](use-log-dump.md)  | When the `UseLogDump` property is `true`, the `LogDump` property stores all messages of type "Information". |
|[LogDump](log-dump.md)  | The `LogDump` property stores all messages of type "Information". This option requires to set the `UseLogDump` property to `true`. |
