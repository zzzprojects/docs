---
id: 5a26a7c8-e8e2-412d-83fc-4ca7d039c462
position: 6
title: How to insert data using a transaction in Dapper?
---

To insert data using a transaction, you have 3 choices:

1. Begin a transaction from your connection with `connection.BeginTransaction()`
2. Create a [TransactionScope](https://learn.microsoft.com/en-us/dotnet/api/system.transactions.transactionscope)
3. Use [Dapper.Transaction](https://dappertutorial.net/dapper-transaction) _(**recommended solution**)_

Learn more about [Transaction with Dapper](https://www.learndapper.com/misc/transaction)