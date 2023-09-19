---
id: 98870d0a-f689-4f90-9e19-8615026d6204
position: 3
title: How to fix in EF Core: dotnet command not found - Could not execute because the specified command or file was not found.
---

<div class="h9">Exception Message:</div>

```
dotnet ef migrations add [MigrationName]
Could not execute because the specified command or file was not found.
Possible reasons for this include:
  * You misspelled a built-in dotnet command.
  * You intended to execute a .NET program, but dotnet-ef does not exist.
  * You intended to run a global tool, but a dotnet-prefixed executable with this name could not be found on the PATH.
```

<div class="image-outer"><img src="/images/efcore/migrations/add-migration/troubleshooting-dotnet-command-not-found.png" loading="lazy" alt="Exception - dotnet command not found"></div>

<div class="h9">Cause:</div>

This error arises when the `dotnet-ef` tool is not installed globally or locally when using .NET Command Line Interface (.NET CLI).

<div class="h9">Solution:</div>

Install the `dotnet-ef` tool:

```cmd
// for the latest version:
dotnet tool install --global dotnet-ef

// for a specific version:
dotnet tool install --global dotnet-ef --version 8.*
dotnet tool install --global dotnet-ef --version 7.*
dotnet tool install --global dotnet-ef --version 6.*
dotnet tool install --global dotnet-ef --version 5.*
dotnet tool install --global dotnet-ef --version 3.*
```
