---
Name: Unknown error 258
LastMod: 2023-09-21
---

# Unknown error 258

One of our customers reported to us a strange timeout error when using our [UpdateFromQuery](/update-from-query) method with SQL Server:

```txt
An error occurred while saving the entity changes. See the inner exception for details.
Execution Timeout Expired. The timeout period elapsed prior to completion of the operation or the server is not responding.
Unknown error 258
```

At the beginning, we were about to suggest the usual solution of increasing the command timeout in EF Core with `context.Database.SetCommandTimeout(300);`. However, upon closer look at the error message, we noticed it ended with `Unknown error 258`, which is not something we usually see.

Upon searching for the `Unknown error 258` error on Google, we found that many other developers have encountered it and that it seems to be triggered for multiple reasons. Most of the time, it is related to people using Linux or Azure.

## Linux

For developers using Linux, we found one cause:

- [MaxThreads](https://support.optimizely.com/hc/en-us/articles/4432366206733-CMS-12-site-crash-due-to-SQL-timeout-error-when-working-in-CMS-edit-mode): Optimizely reports that this error occurs because `ThreadPool.MaxThreads = 8` is the default setting, and the application needs more threads.

## Azure

For developers using Azure (or even Azure on Linux), we found three causes:

- [Azure Max DTU](https://github.com/dotnet/SqlClient/issues/1530#issuecomment-1089243719): [oyvos](https://github.com/oyvost) mentioned that this error commonly occurs when Azure reaches the DTU limit.
- [MARS Enabled](https://github.com/dotnet/SqlClient/issues/422): [pawelpabich](https://github.com/pawelpabich) mentioned having this issue when he has MARS enabled. In his case, he simply turned it off.
- [Transaction Scope](https://github.com/dotnet/SqlClient/issues/647#issuecomment-1602980797): [tobyreid](https://github.com/tobyreid) mentioned this issue was due to using `TransactionScope`. In his case, he removed the `TransactionScope`, although increasing the command timeout might also fix it.

## Conclusion

While it's clear that this exception is not caused by Entity Framework Extensions, it appears to have multiple causes and solutions you could try. More causes could easily be found by searching for this exception message on Google.

Unfortunately, our customer never got back to us, so we are not sure what the solution was for him. On the bright side, he probably found his solution among those we suggested.

If you find another cause worth mentioning regarding the `Unknown error 258` timeout, feel free to let us know so we can improve this article: [Contact Us](/contact-us)