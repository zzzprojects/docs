---
Name: Security
---

# Security

Html Agility Pack can parse HTML from untrusted sources. When processing such content, it is recommended to configure limits to prevent excessive resource consumption and potential denial-of-service attacks.

## Maximum Nesting Depth

HTML documents containing deeply nested elements can cause stack overflow exceptions or excessive processing time.

To prevent this, Html Agility Pack provides two options to limit nesting depth:

- **OptionMaxNestedChildNodes**: Limits the nesting depth when parsing HTML.
- **MaxDepthLevel**: Limits the nesting depth when accessing or writing HTML nodes.

### Example

```csharp
// Limit nesting depth during parsing
var doc = new HtmlDocument
{
    OptionMaxNestedChildNodes = 100
};

// Limit nesting depth during node operations
HtmlDocument.MaxDepthLevel = 100;

doc.LoadHtml(html);

var text = doc.DocumentNode.InnerText;
```

## Recommendations

- Set appropriate nesting limits when processing untrusted HTML.
- Use a lower limit for applications exposed to user-submitted content.
- Adjust the limits based on your application's requirements.

**Note:** By default, these limits are not enforced. Applications processing untrusted HTML should configure them explicitly.