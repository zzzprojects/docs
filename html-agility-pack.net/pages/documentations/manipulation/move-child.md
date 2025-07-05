---
Name: MoveChild Method
---

# MoveChild

## public HtmlNode MoveChild(HtmlNode child)

Moves a node already associated and appends it to this node instead (must be from a different document). MoveChild method is a member of **HtmlAgilityPack.HtmlNode**

### Parameters:

child: The child node to move.

### Example

The following example will move a child from one document to another one.

```csharp
var html1 ="<body></body>";
var htmlDoc1 = new HtmlDocument();
htmlDoc1.LoadHtml(html1);

var html2 = "<div>Hi ZZZ Projects</div>";
var htmlDoc2 = new HtmlDocument();
htmlDoc2.LoadHtml(html2);

var body = htmlDoc1.DocumentNode.ChildNodes[0];
var div = htmlDoc2.DocumentNode.ChildNodes[0];

Console.WriteLine("Html Before: " + htmlDoc1.DocumentNode.InnerHtml);

body.MoveChild(div);

Console.WriteLine("Html AFTER: " + htmlDoc1.DocumentNode.InnerHtml);
```

Click [here](https://dotnetfiddle.net/ti20jO) to run this example.