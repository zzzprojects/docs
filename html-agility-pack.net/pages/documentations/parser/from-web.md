---
Name: Load from Web Method
---

# From Web

## Load HTML From Web

HtmlWeb.Load method retrieves an HTML document from an internet resource.

### Example

The following example loads HTML from the web.

```csharp

var html = @"https://html-agility-pack.net/";

HtmlWeb web = new HtmlWeb();

var htmlDoc = web.Load(html);

var node = htmlDoc.DocumentNode.SelectSingleNode("//head/title");

Console.WriteLine("Node Name: " + node.Name + "\n" + node.OuterHtml);

```

Click [here](https://dotnetfiddle.net/Vtwi7g) to run this example.
