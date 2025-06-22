---
Name: DisableAllFilters
---

# DisableAllFilters

## Description

DisableAllFilters method disables all filters within a DbContext at once which are globally enabled. 


```csharp

context.DisableAllFilters();

```

Disabling globally enabled filters will apply only to that DbContext and it will not affect any other DbContext instances.


