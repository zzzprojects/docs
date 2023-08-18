---
title: Dapper Relationship, SplitOn, One-To-Many, One-to-One
description: Dapper supports mapping all kinds of relationships using the SplitOn parameter to map your One-To-Many, One-To-One, or Many-to-Many relation.
canonical: relationships
status: Published
lastmod: 2023-01-05T12:53:42Zf
---

# Using Relationships With Dapper

Full-featured ORMs like [Entity  Framework Core](https://www.learnentityframeworkcore.com/relationships) have been designed to understand and work with relationships. EF Core will ensure that any retrieved data is automatically mapped to the appropriate association or navigational property if a related entity is included as part of a query. 

 - In SQL, you have a choice in how you go about retrieving related data. 
 - You can either perform one query to obtain all the data (a standard `JOIN` query), or you can perform multiple queries, one to get the parent data, and another to obtain related data. 
 - For most scenarios, executing a `JOIN` query is likely the right choice. 
 - Each record returned from a `JOIN` query will include data from multiple objects. 

## Dapper Relationships

Dapper provides a feature called **Multi mapping** to map data to multiple objects explicitly and nested objects. As a result, Dapper is an excellent tool for managing relationships between entities within your database that can be used to retrieve related data from the database. 

 - It supports both synchronous and asynchronous operation, allowing you to use it in various scenarios. 
 - Using Dapper, you can easily execute [stored procedures](/stored-procedures), map results to strongly typed objects or dynamic objects, and execute [bulk CRUD operations](/bulk-operations). 
 - Dapper provides extension methods that enable us to perform queries on nested objects without having to write separate SQL statements for each object. 
 - For example, you can select from multiple tables in a single dapper query. 
 - This makes it easier and faster to retrieve related entities without having to write complex SQL statements.
 
## Dapper SplitOn

Dapper supports multi-mapping, which allows you to map a single row to multiple objects. It is helpful if you need to retrieve data from multiple tables in a single query.

 - To do this, we need to use the Dapper `splitOn` parameter when calling the `Query` method.
 - The `splitOn` parameter tells Dapper what column(s) to use to split the data into multiple objects.

```
using(var connection = new SqlConnection(connectionString))
{
    var sql = @"SELECT ProductID, ProductName, p.CategoryID, CategoryName
                FROM Products p 
                INNER JOIN Categories c ON p.CategoryID = c.CategoryID";
				
    var products = await connection.QueryAsync<Product, Category, Product>(sql, (product, category) => {
        product.Category = category;
        return product;
    }, 
    splitOn: "CategoryId" );
	
    products.ToList().ForEach(product => Console.WriteLine($"Product: {product.ProductName}, Category: {product.Category.CategoryName}"));
	
    Console.ReadLine();
}
```

The `splitOn` argument tells Dapper to split the data on the `CategoryId` column. Anything up to that column maps to the first parameter `Product`, and anything else from that column onward should be mapped to the second input parameter `Category`.

## Dapper One-To-Many Relationships

One-to-many relationships are very common in software development. For example, it defines relationships between two entities where one entity has a primary key, and the other contains foreign keys.

The following class definitions represent a cut-down version of the `Product` and `Category` entities from the canonical Northwind sample database:

```csharp
public class Product
{
    public int ProductID { get; set; }
    public string ProductName { get; set; }
    ...
    public Category Category { get; set; }
}

public class Category
{
    public int CategoryID { get; set; }
    public string CategoryName { get; set; }
    ...
    public ICollection<Product> Products { get; set; }
}
```

There is an association between the `Product`  and `Category` classes, represented by the `Category` property in the `Product` class and the `Products` property in the `Category` class. That means a one-to-many relationship. A product can have one category, and a category can have many products. 

![One To Many Relationship](/images/03-05-2019-08-18-16.png)



The following example shows an SQL query that retrieves the category related to each product from the Northwind database:

```sql
SELECT ProductID, ProductName, p.CategoryID, CategoryName 
FROM Products p 
INNER JOIN Categories c ON p.CategoryID = c.CategoryID
```
When this SQL is executed, the category information is included with each product:

![Image](/images/02-05-2019-09-18-27.png)

That is how Dapper can execute that query to map multiple products in one pass:

```
using(var connection = new SqlConnection(connectionString))
{
    var sql = @"SELECT ProductID, ProductName, p.CategoryID, CategoryName
                FROM Products p 
                INNER JOIN Categories c ON p.CategoryID = c.CategoryID";
				
    var products = await connection.QueryAsync<Product, Category, Product>(sql, (product, category) => {
        product.Category = category;
        return product;
    }, 
    splitOn: "CategoryID" );
	
    products.ToList().ForEach(product => Console.WriteLine($"Product: {product.ProductName}, Category: {product.Category.CategoryName}"));
	
    Console.ReadLine();
}
```
When the code above is executed, the products are output along with their related categories:

![Image](/images/02-05-2019-08-32-02.png)

Multi-mapping is implemented as a `Func` generic delegate. There are overloads to the `Query` and `QueryAsync` methods that take multiple generic parameters and a `Func<TFirst, ..., TReturn> map` argument. The last parameter represents the return type, while the others are input parameters to be processed within the body of the `Func` delegate, which is a mapping function. The mapping function specifies how the resulting data should be mapped to the return type.

In this example, the input parameters are `Product` and `Category`, and the return type is `Product` (`<Product, Category, Product>`). So we are telling the `QueryAsync` method to take a product and a category, process them in some way to be defined and return a product.

The mapping function in this instance is quite clear:

```csharp
(product, category) => {
    product.Category = category;
    return product;
}
```

Dapper maps data to the first type in the same way as it does if only one generic parameter has been supplied to the `QueryAsync<T>` method. It is then told to map data to the `Category` type and to assign the resulting object to the product's `Category` property. 

How does Dapper identify a `Category` object from the data? Notice the `splitOn` argument? That tells Dapper to split the data on the `CategoryId` column. Anything up to that column maps to the first parameter (the `Product`), and anything else from that column onward should be mapped to the second input parameter (the `Category`). 


## Dapper Many To Many Relationships

Multiple mapping also works with many-to-many relationships. The following diagram shows a many-to-many relationship between the `Post` entity and a `Tag` entity in a model relating to a Blog application:

![Many To Many Relationship](/images/03-05-2019-08-38-42.png)

That is how the associations are represented in the code:

```csharp
public class Tag
{
    public int TagId { get; set; }
    public string TagName { get; set; }
    public List<Post> Posts { get; set; }
}

public class Post
{
    public int PostId { get; set; }
    public string Headline { get; set; }
    public string Content { get; set; }
    public Author Author { get; set; }
    public List<Tag> Tags { get; set; } 
}
```

The `Tag` class has a collection property representing many posts, and the `Post` class includes a collection property representing many tags.

Many to many relationships are represented in the database schema as a separate JOIN table:


![Many To Many Relationship Join Table](/images/03-05-2019-08-53-30.png)

The following SQL retrieves the tag information related to each post:

```sql
SELECT p.PostId, Headline, t.TagId, TagName
FROM Posts p 
INNER JOIN PostTags pt ON pt.PostId = p.PostId
INNER JOIN Tags t ON t.TagId = pt.TagId
```

A row is returned for each tag, resulting in duplication of post information:

![Image](/images/13-05-2019-09-17-34.png)

You use multi-mapping to populate both entities in the same way as previously, but this time, you use a grouping function on the result to combine the duplicate post instances and the tags:

```csharp
using(var connection = new SqlConnection(connectionString))
{
    var sql = @"SELECT p.PostId, Headline, t.TagId, TagName
                FROM Posts p 
                INNER JOIN PostTags pt ON pt.PostId = p.PostId
                INNER JOIN Tags t ON t.TagId = pt.TagId";
				
    var posts = await connection.QueryAsync<Post, Tag, Post>(sql, (post, tag) => {
        post.Tags.Add(tag);
        return post;
    }, splitOn: "TagId");
	
    var result = posts.GroupBy(p => p.PostId).Select(g =>
    {
        var groupedPost = g.First();
        groupedPost.Tags = g.Select(p => p.Tags.Single()).ToList();
        return groupedPost;
    });
	
    foreach(var post in result)
    {
        Console.Write($"{post.Headline}: ");
		
        foreach(var tag in post.Tags)
        {
            Console.Write($" {tag.TagName} ");
        }
		
        Console.Write(Environment.NewLine);
    }
}
```
Then the tags (highlighted in yellow) are output along with the post headline:

![Image](/images/14-05-2019-08-17-09.png)

## Dapper Multiple Relationships

The multi-mapping works with multiple relationships. In this example, the author's detail is included in the query to be executed against the database:

```sql
SELECT p.PostId, Headline, FirstName, LastName, t.TagId, TagName
FROM Posts p 
INNER JOIN Authors a ON p.AuthorId = a.AuthorId
INNER JOIN PostTags pt ON pt.PostId = p.PostId
INNER JOIN Tags t ON t.TagId = pt.TagId
```

The multi-mapping function takes an additional input parameter representing the extra entity being retrieved:

```csharp
using(var connection = new SqlConnection(connectionString))
{
    var sql = @"SELECT p.PostId, Headline, FirstName, LastName, t.TagId, TagName
                FROM Posts p 
                INNER JOIN Authors a ON p.AuthorId = a.AuthorId
                INNER JOIN PostTags pt ON pt.PostId = p.PostId
                INNER JOIN Tags t ON t.TagId = pt.TagId";
				
    var posts = await connection.QueryAsync<Post, Author, Tag, Post>(sql, (post, author, tag) => {
        post.Author = author;
        post.Tags.Add(tag);
        return post;
    }, splitOn: "FirstName, TagId");
	
    var result = posts.GroupBy(p => p.PostId).Select(g =>
    {
        var groupedPost = g.First();
        groupedPost.Tags = g.Select(p => p.Tags.Single()).ToList();
        return groupedPost;
    });
	
    foreach(var post in result)
    {
        Console.Write($"{post.Headline}: ");
		
        foreach(var tag in post.Tags)
        {
            Console.Write($" {tag.TagName} ");
        }
		
        Console.Write($" by {post.Author.FirstName} {post.Author.LastName} {Environment.NewLine}");
    }
}
```
![Image](/images/14-05-2019-08-53-11.png)