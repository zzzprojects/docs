---
Name: MoveChildren Method
---

# MoveChildren

## public void MoveChildren(HtmlNodeCollection children)

Move a children collection already associated and append it to this node instead (must be from a different document). MoveChildren method is a member of **HtmlAgilityPack.HtmlNode**

### Parameters:

children: The children collection already associated to move to another node.

### Example

The following example will move all children from a HTML body to another HTML body (in a different document).

```csharp
var html1 ="<body></body>";
var htmlDoc1 = new HtmlDocument();
htmlDoc1.LoadHtml(html1);

var html2 = "<body><div>Hi ZZZ Projects</div><div>Website: <a href='https://zzzprojects.com/'>https://zzzprojects.com/</a></div></body>";
var htmlDoc2 = new HtmlDocument();
htmlDoc2.LoadHtml(html2);

var body1 = htmlDoc1.DocumentNode.ChildNodes[0];
var body2 = htmlDoc2.DocumentNode.ChildNodes[0];

Console.WriteLine("Html Before: " + htmlDoc1.DocumentNode.InnerHtml);

body1.MoveChildren(body2.ChildNodes);

Console.WriteLine("Html AFTER: " + htmlDoc1.DocumentNode.InnerHtml);
```

Click [here](https://dotnetfiddle.net/AFLqmR) to run this example.