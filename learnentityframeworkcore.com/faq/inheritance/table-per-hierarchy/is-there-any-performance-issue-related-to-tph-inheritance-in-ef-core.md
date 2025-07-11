---
id: 03588cfd-3c16-48bf-bd6d-f1fd86e2d301
position: 5
title: Is there any performance issue related to TPH inheritance in EF Core?
---

The `TPH` inheritance generally offers good performance for read operations due to fewer joins and a simpler querying model than other inheritance strategies. Write operations are also usually very fast since only 1 table is used.
