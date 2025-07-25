---
Name: Element Method
---

# Element

## public HtmlNode Element(string name)

Gets the first-generation child node with a matching name. The Element method is a member of **HtmlAgilityPack.HtmlNode**

### Parameters:

name: The name of the child node.

### Returns:

Returns the first-generation child node with a matching name.

### Example

The following example displays the first-generation child node with a matching name.

```csharp

var htmlDoc = new HtmlDocument();
htmlDoc.LoadHtml(html);

var htmlBody = htmlDoc.DocumentNode.SelectSingleNode("//body");

var node = htmlBody.Element("p");

Console.WriteLine(node.OuterHtml);

```

Click [here](https://dotnetfiddle.net/MhyWsN) to run this example.
