---
Permalink: ef6-query-include-filter-load-multiple-level
CanonicalLink: https://entityframework-plus.net/ef-core-query-include-filter-load-multiple-level
---

# Query IncludeFilter - Load multiple levels

{% include template-example.html %} 
```csharp

// using Z.EntityFramework.Plus; // Don't forget to include this.
var ctx = new EntitiesContext();

// LOAD blogs and related active posts and comments.
var blogs = ctx.Blogs.IncludeFilter(x => x.Posts.Where(y => !y.IsSoftDeleted))
                     .IncludeFilter(x => x.Posts.Where(y => !y.IsSoftDeleted)
                                                .Select(y => y.Comments
                                                              .Where(z => !z.IsSoftDeleted)))
                     .ToList();

```
[Try it](https://dotnetfiddle.net/gFGxt6)
