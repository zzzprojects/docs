---
Title: What is Dapper Plus? Join Thousands of Companies Boosting Performance Worldwide
MetaDescription: Learn what Dapper Plus is, who uses this library, why you need this library, how it helps you to develop faster, and how Dapper Plus helps Dapper.
LastMod: 2024-09-17
---

# What is Dapper Plus? Join Thousands of Companies Boosting Performance Worldwide

When I discovered [Dapper](https://www.learndapper.com/), I fell in love with this library as it was making my life so much easier. I only had to write my query and **BOOM!** Dapper was handling everything for me... From opening the connection, creating/executing the command, to mapping the result.

Like every library, I eventually discovered that Dapper also has its limitations.

One day, the job I had to do was to import more than 10,000 rows into my database. Since Dapper already supports [inserting multiple rows in a database](https://www.learndapper.com/saving-data/insert#dapper-insert-multiple-rows), the job was already completed, right? **WRONG!** It was at this moment I discovered that Dapper has performance limitations by making one database round-trip for every entity I had to save instead of bulking them.

Obviously, I didn't want my end user to have to wait several seconds for these import operations, anyway who would want to wait that long? I barely had the patience myself to wait that long when I was coding this import feature.

This is when and why I created Dapper Plus with the same goal as Dapper in my mind. So **What is Dapper Plus?** A library you can use with or without Dapper that makes your life easier by providing an easy-to-use API that extends your connection. Dapper Plus handles everything for you... From opening the connection, creating/executing the most optimized saving command, to outputting values to your entities.

The most well-known methods are our bulk operations:
- [BulkInsert](/bulk-insert)
- [BulkUpdate](/bulk-update)
- [BulkDelete](/bulk-delete)
- [BulkMerge](/bulk-merge)
- [BulkSynchronize](/bulk-synchronize)

Dapper Plus is very easy to get started with, here is an example of how quickly it is:

```csharp
var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServer());
		
// Easy to use
connection.BulkInsert(products);

// Easy to customize
connection.UseBulkOptions(options => options.InsertIfNotExists = true)
		  .BulkInsert(products);
```

Yup, the code speaks for itself!

- The first `BulkInsert` will insert all products into your database.
- The second `BulkInsert` will insert only products that don't already exist.

Click on this [Online Example](https://dotnetfiddle.net/OxvKDQ) link and see this code in action.

You will see through our documentation, that we offer links so you can directly try the code live on [.NET Fiddle](https://dotnetfiddle.net/). You can also browse all our examples and those created by the community in our [Online Examples](/online-examples) section.

## Who Uses This Library? Anyone and Everyone!

Me, maybe you, banks, governments, university, etc.! Over the years, all our libraries including [Entity Framework Extensions](https://entityframework-extensions.net/) and [C# Eval Expression](https://eval-expression.net/) have reached more than **5000 happy paid customers** in all types of industries, including many in the Fortune 100. I mean, we even have two well-known space agencies that use our library. So when our library is used even for space programs, we are literally everywhere!

Our offer is simple: **providing the best performance and flexibility**.

Why do so many companies trust us? Probably because we are proud to offer an **outstanding customer support**. We've offered bulk extension methods through our libraries since 2014 and we keep actively maintaining them.

## Why You Need This Library to Reduce Your Saving Time by up to 99%

We said earlier that Dapper Plus was faster than the traditional way of inserting with Dapper, but how much faster exactly?

| Library      | 500 rows   | 1,000 rows | 2,000 rows  |
| :----------- | ---------: | ---------: | ----------: |
| Dapper       | 1550 ms    | 3250 ms    | 6500 ms     |
| Dapper Plus  | 40 ms      | 50 ms      | 80 ms       |

**You don't believe our numbers?** Why don't you try it yourself by running this [Online Benchmark](https://dotnetfiddle.net/kz4UpX)? You simply have to open the link, click on the "Run" button, and let the numbers speak for themselves!

This means that Dapper Plus for inserting 2,000 rows is around **75x faster, reducing time by 99%**. WOW!

Some developers will argue that you can use alternative techniques to insert faster than the traditional ways in Dapper (obviously, since we use those techniques!), but **remember:** One of the major advantages of the Dapper Plus library is making your life easier.

If you ask those same developers how to write a merge (insert or update) operation, it will take them several minutes, and they will even need to search on Google how to make a MERGE statement (I know I still have to do it every time). On the other hand, you can simply call [BulkMerge](/bulk-merge) and have access to [hundreds of options](/options). So even for explaining and writing code, we are still way faster!

Saving your data as fast as possible is very important to make your application **responsive**, and that's exactly what our library offers.

## Who Else Wants to Develop Faster With More Flexibility?

Unlike Dapper, a major difference with Dapper Plus is that you don't have to write your own SQL; the library handles everything for you!

On my side, when I need to query my database, I **WANT** to write my own SQL (at least the complex ones). I can ensure only the minimum columns are selected, and the `SELECT` statement is optimized, especially if I need to `JOIN` multiple tables or need to perform some `GROUP BY` operations. Then, I let Dapper handle perfectly all the rest for me.

However, I find writing my `INSERT`, `UPDATE`, and `DELETE` statements boring as they are pretty much always the same pattern, with just a few different options.

So, letting Dapper Plus write those saving SQL statements for you comes with some major advantages:

- **Ease of Development:** The code is faster to write, increasing your efficiency.
- **Ease of Maintenance:** The code is easier to read and modify, reducing the learning curve for new developers.
- **Increased Accuracy:** The code is already well tested, reducing the chance of human error.
- **Increased Scalability:** The code is coded to be optimized, increasing your application's responsiveness for your clients.

There are multiple other advantages. But, in short, your code is faster and **YOU** code faster, allowing yourself to focus more on strategic tasks that require **your expertise** and less about writing those boring SQL statements.

While performance is always the main goal for Dapper Plus, providing you with flexibility is another. You will learn that Dapper Plus gives you access to [hundreds of options](/options) enhancing even more those advantages that we just saw. Even when you looked at our first example, you understood in a blink of an eye what was the purpose of this option `InsertIfNotExists = true`.

We always like to say that our library is: **Easy to use, easy to customize!**

## Did You Know? We Are a Major Sponsor of Dapper

Our company [ZZZ Projects](https://zzzprojects.com/) has been a major contributor to Dapper since 2014 by creating and maintaining the best tutorials about it:

- [Learn Dapper](https://www.learndapper.com/)
- [Dapper Tutorial](https://dappertutorial.net/)

However, we felt that was not enough. Let's be honest, even if our tutorials have played maybe a small part in the growing popularity of Dapper, in exchange Dapper has contributed without any doubt to the success of Dapper Plus.

Since 2024, our company is proud to be a [major sponsor of Dapper](https://dapperlib.github.io/Dapper/dapperplus), ensuring that this library can continue to be **actively maintained** and remain **100% free**.

So, purchasing Dapper Plus directly benefits and helps Dapper as well. You can learn more about our mission to [add value to the .NET Community](https://zzzprojects.com/mission).

## Conclusion

In this overview, we've only just begun to explore the potential of Dapper Plus. We now understand more about how Dapper Plus relates to Dapper and how both libraries mutually benefit each other.

We have seen a very basic scenario and observed that our library is so user-friendly that you can [download it](/download) and start using it right away.

It's now time to continue by learning the basic of Dapper Plus with our [Getting Started](getting-started) article.