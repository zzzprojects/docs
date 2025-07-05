---
Name: Load from Browser Method
---

# From Browser

## Load HTML From Browser

HtmlWeb.Load method retrieves an HTML document from a web browser. It makes it possible to wait for JavaScript to be run by customizing the isBrowserScriptCompleted parameter. 

### Example

The following example loads an HTML from the WebBrowser and waits until the text is set for the DIV.

```csharp
using System.Windows.Forms;

string url = "https://html-agility-pack.net/from-browser";

var web1 = new HtmlWeb();
var doc1 = web1.LoadFromBrowser(url, o =>
{
	var webBrowser = (WebBrowser) o;

	// WAIT until the dynamic text is set
	return !string.IsNullOrEmpty(webBrowser.Document.GetElementById("uiDynamicText").InnerText);
});
var t1 = doc1.DocumentNode.SelectSingleNode("//div[@id='uiDynamicText']").InnerText;

var web2 = new HtmlWeb();
var doc2 = web2.LoadFromBrowser(url, html =>
{
	// WAIT until the dynamic text is set
	return !html.Contains("<div id=\"uiDynamicText\"></div>");
});
var t2 = doc2.DocumentNode.SelectSingleNode("//div[@id='uiDynamicText']").InnerText;

Console.WriteLine("Text 1: " + t1);
Console.WriteLine("Text 2: " + t2);

```

<div id="uiDynamicText"></div>

<script>
setTimeout(function(){ document.getElementById("uiDynamicText").innerHTML = "Dynamic Text for Example"; }, 1000);
</script>

### Note

This method is not available for .NET Core.

We recommend using [Selenium WebDriver](https://riptutorial.com/selenium-webdriver/learn/100000/overview)
