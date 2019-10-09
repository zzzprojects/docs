---
PermaID: 1000143
Name: Dot Net Blog
---

# .NET Blog

@using Z.Websites.Web.Models
@{
    var model = (EntityFrameworkFaq)ViewBag.EntityFrameworkFaq;
}

<h2>.Net Blog</h2>

<table>
    <tbody>
        @foreach (var blog in model.Blogs)
        {
            if (blog.Kind == ".Net")
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