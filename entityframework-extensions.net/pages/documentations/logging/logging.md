# Logging

## Description

The Entity Framework Extensions `Logging` feature let you log all messages of type "Information".


## Key Features

- Allow to see SQL that are executed
- Allow to see parameter & time...

## Getting Started

There is 2 ways to use the logging features

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
context.BulkMerge(list, options =>
{
    options.UseLogDump = true;
    options.LogDump = sb;
    
});

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
