---
PermaID: 1000132
Name: Why Async method are slow
---

# Why EF Async Methods are Slow

## Why EF async methods are slower than non-async? 

Why Entity Framework async operation takes too much time as compared to a non-async operation?

### StackOverflow Related Questions

 - [Entity Framework async operation takes ten times as long to complete](https://stackoverflow.com/questions/28543293/entity-framework-async-operation-takes-ten-times-as-long-to-complete)

## Answer

The asynchronous version will always be slower than the synchronous version when there is no concurrency. 

 - It's doing all of the same work as the non-async version, but with a small amount of overhead added to manage the asynchrony.
 - Each request will be slower, but if you make 1000 requests at the same time, the asynchronous implementation will be able to handle them all more quickly (at least in certain circumstances).
 - It happens because the asynchronous solution allows the thread that was allocated to handle the request to go back to the pool and handle other requests, whereas the synchronous solution forces the thread to sit there and do nothing while it waits for the asynchronous operation to complete. 
 - There is overhead in structuring the program in a way that allows the thread to be freed up to do other work, but the advantage is the ability of that thread to do another job. 
 - If your program has no other work for that thread, then it ends up being a net loss.
 - Async/await saves time only when the job is I/O-bound. Any application's job that is CPU-bound will introduce some performance hits. 
 - That's because if you have some computations that take 10s on your CPU(s), then adding async/await will add X extra time to that 10s for task creation, scheduling and synchronization to get the job done.
 - The performance often hits due to introducing async/await are not that large especially if you are careful not to overdo it.