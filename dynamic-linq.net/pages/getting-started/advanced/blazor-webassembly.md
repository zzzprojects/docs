---
Permalink: advanced-blazor-webassembly
Name: Blazor WebAssembly
---

# Blazor WebAssembly

It is possible to use the Dynamic LINQ (`System.Linq.Dynamic.Core`) library in a Blazor WebAssembly client project.

However you can run into the issues as described below, read further on how to fix this.


## Issue(s)

When running the Blazor WebAssembly client project in **Release** mode, you can get errors in the console like this:
``` js
blazor.webassembly.js:1 crit: Microsoft.AspNetCore.Components.WebAssembly.Rendering.WebAssemblyRenderer[100]
      Unhandled exception rendering component: Operator '&&' incompatible with operand types 'Boolean' and 'Boolean'
Operator '&&' incompatible with operand types 'Boolean' and 'Boolean' (at index 21)
```

## Solution

The solution for these issue is in most cases to add the following line to the `.csproj` client project:
``` xml
<BlazorWebAssemblyEnableLinking>false</BlazorWebAssemblyEnableLinking>
```

If you want more fine-grained control on which assembles are linked, see [this link](https://docs.microsoft.com/en-us/aspnet/core/blazor/host-and-deploy/configure-linker?view=aspnetcore-3.1#control-linking-with-a-configuration-file).