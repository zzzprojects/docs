---
PermaID: 1000141
Name: General Blog
---

# General's Blog

@using Z.Websites.Web.Models
@{
    var model = (EntityFrameworkFaq)ViewBag.EntityFrameworkFaq;
}

<h2>General's Blog</h2>

<table>
    <tbody>
        @foreach (var blog in model.Blogs)
        {
            if (blog.Kind == "General")
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