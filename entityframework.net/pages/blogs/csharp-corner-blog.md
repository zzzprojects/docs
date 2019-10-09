---
PermaID: 1000146
Name: Csharp Corner Blog
---

# CSharpCorner's Blog

@using Z.Websites.Web.Models
@{
    var model = (EntityFrameworkFaq)ViewBag.EntityFrameworkFaq;
}

<h2>CSharpCorner's Blog</h2>

<table>
    <tbody>
        @foreach (var blog in model.Blogs)
        {
            if (blog.Kind == "CSharpCorner")
            {
                <tr>
                    <td>
                        <h3><a href="@blog.Url">@blog.Title</a></h3>
                        @blog.Description
                    </td>
                </tr>
            }
        }
    </tbody>
</table>