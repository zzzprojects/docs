# SqlBulkCopy - Recommended BatchSize

## Question
What's the recommended batch size?

## Answer
Our answer: **4000**

Unfortunately, there is no answer that fit all scenario. So there is no perfect answer to this question.

There are only two rules to follow:

- NOT too high
- NOT too low

There is so much thing that can impact the recommended batch size:

- Index
- Lock
- Row Size
- Server latency
- Server load
- Trigger
- And more...

### How can I find the optimal value
It doesn't worth it. SqlBulkCopy is already VERY fast and losing time to trying to improve will only get you a small gain of 10%? Which you may loose in some other case when some case has less or more row to insert.

The best recommendation you can get is to choose a value within your company and stick with it. Your time will be better invested elsewhere.

### But I have read in another article that XYZ is the recommended value
In this article, the XYZ recommended value without a doubt followed the "NOT too high, NOT too low" rule.

So yes, this is also an excellent recommended value!