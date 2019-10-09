---
PermaID: 1000140
Name: Gil Fink Blog
---

# Gil Fink's Blog

@using Z.Websites.Web.Models
@{
    var model = (EntityFrameworkFaq)ViewBag.EntityFrameworkFaq;
}

<h2>Gil Fink's Blog</h2>

<table>
    <tbody>
        @foreach (var blog in model.Blogs)
        {
            if (blog.Kind == "GilFink")
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