---
PermaID: 1000137
Name: One Unicorn Blog
---

# OneUnicorn's Blog

@using Z.Websites.Web.Models
@{
    var model = (EntityFrameworkFaq)ViewBag.EntityFrameworkFaq;
}

<h2>OneUnicorn's Blog</h2>

<table>
    <tbody>
        @foreach (var blog in model.Blogs)
        {
            if (blog.Kind == "OneUnicorn")
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