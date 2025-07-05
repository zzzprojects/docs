---
Name: SelectSingleNode Method
---

# HTML SelectSingleNode

## SelectSingleNode Method

Selects the first HtmlNode matching the HtmlAgilityPack.HtmlNode.XPath expression.

### Parameters:

xpath: The XPath expression. May not be null.

### Returns:

The first HtmlAgilityPack.HtmlNode that matches the XPath query or a null reference if no matching node was found.

### Examples

The following example selects the first node matching the XPath expression using the SelectSingleNode method.

```csharp

var htmlDoc = new HtmlDocument();
htmlDoc.LoadHtml(html);

string name = htmlDoc.DocumentNode
    .SelectSingleNode("//td/input")
    .Attributes["value"].Value;

```

Click [here](https://dotnetfiddle.net/KHzARJ) to run this example.

