---
PermaID: 1000147
Name: Blogs
---

# Blogs

@using Z.Websites.Web.Models
@{
    var model = (EntityFrameworkFaq) ViewBag.EntityFrameworkFaq;
}

<h2>Devmate's Blog</h2>

<table>
    <tbody>
    @foreach (var blog in model.Blogs)
    {
        if (blog.Kind == "Devmate" && blog.Pinned == "1")

        {
            <tr>
                <td>
                    <h3>
                        <a href="@blog.Url">@blog.Title</a>
                    </h3>
                    @blog.Description
                </td>
            </tr>
        }
    }
    </tbody>
</table>

<a href="/devmate-blog">See more Devmate's posts...</a>

<h2>Dixin's Blog</h2>

<table>
    <tbody>
    @foreach (var blog in model.Blogs)
    {
        if (blog.Kind == "Dixin" && blog.Pinned == "1")
        {
            <tr>
                <td>
                    <h3>
                        <a href="@blog.Url">@blog.Title</a>
                    </h3>
                    @blog.Description
                </td>
            </tr>
        }
    }
    </tbody>
</table>

<a href="/dixin-blog">See more Dixin's posts...</a>

<h2>.Net Blog</h2>

<table>
    <tbody>
    @foreach (var blog in model.Blogs)
    {
        if (blog.Kind == ".Net" && blog.Pinned == "1")
        {
            <tr>
                <td>
                    <h3>
                        <a href="@blog.Url">@blog.Title</a>
                    </h3>
                    @blog.Description
                </td>
            </tr>
        }
    }
    </tbody>
</table>

<a href="/dot-net-blog">See more posts...</a>

<h2>Tony Sneed's Blog</h2>

<table>
    <tbody>
    @foreach (var blog in model.Blogs)
    {
        if (blog.Kind == "TonySneed" && blog.Pinned == "1")
        {
            <tr>
                <td>
                    <h3>
                        <a href="@blog.Url">@blog.Title</a>
                    </h3>
                    @blog.Description
                </td>
            </tr>
        }
    }
    </tbody>
</table>

<a href="/tony-sneed-blog">See more TonySneed's posts...</a>

<h2>JetBrains Blog</h2>

<table>
    <tbody>
    @foreach (var blog in model.Blogs)
    {
        if (blog.Kind == "JetBrains" && blog.Pinned == "1")
        {
            <tr>
                <td>
                    <h3>
                        <a href="@blog.Url">@blog.Title</a>
                    </h3>
                    @blog.Description
                </td>
            </tr>
        }
    }
    </tbody>
</table>

<a href="/jet-brains-blog">See more JetBrains's posts...</a>

<h2>Telerik's Blog</h2>

<table>
    <tbody>
    @foreach (var blog in model.Blogs)
    {
        if (blog.Kind == "Telerik" && blog.Pinned == "1")
        {
            <tr>
                <td>
                    <h3>
                        <a href="@blog.Url">@blog.Title</a>
                    </h3>
                    @blog.Description
                </td>
            </tr>
        }
    }
    </tbody>
</table>

<a href="/telerik-blog">See more Telerik's posts...</a>

<h2>CSharpCorner's Blog</h2>

<table>
    <tbody>
    @foreach (var blog in model.Blogs)
    {
        if (blog.Kind == "CSharpCorner" && blog.Pinned == "1")
        {
            <tr>
                <td>
                    <h3>
                        <a href="@blog.Url">@blog.Title</a>
                    </h3>
                    @blog.Description
                </td>
            </tr>
        }
    }
    </tbody>
</table>

<a href="/csharp-corner-blog">See more CSharpCorner's posts...</a>

<h2>OneUnicorn's Blog</h2>

<table>
    <tbody>
    @foreach (var blog in model.Blogs)
    {
        if (blog.Kind == "OneUnicorn" && blog.Pinned == "1")
        {
            <tr>
                <td>
                    <h3>
                        <a href="@blog.Url">@blog.Title</a>
                    </h3>
                    @blog.Description
                </td>
            </tr>
        }
    }
    </tbody>
</table>

<a href="/one-unicorn-blog">See more OneUnicorn's posts...</a>

<h2>MitchelSellers's Blog</h2>

<table>
    <tbody>
    @foreach (var blog in model.Blogs)
    {
        if (blog.Kind == "MitchelSellers" && blog.Pinned == "1")
        {
            <tr>
                <td>
                    <h3>
                        <a href="@blog.Url">@blog.Title</a>
                    </h3>
                    @blog.Description
                </td>
            </tr>
        }
    }
    </tbody>
</table>

<a href="/mitchel-sellers-blog">See more MitchelSellers's posts...</a>

<h2>eCanarys's Blog</h2>

<table>
    <tbody>
    @foreach (var blog in model.Blogs)
    {
        if (blog.Kind == "eCanarys" && blog.Pinned == "1")
        {
            <tr>
                <td>
                    <h3>
                        <a href="@blog.Url">@blog.Title</a>
                    </h3>
                    @blog.Description
                </td>
            </tr>
        }
    }
    </tbody>
</table>

<a href="/ecanarys-blog">See more eCanarys's posts...</a>

<h2>Gil Fink's Blog</h2>

<table>
    <tbody>
    @foreach (var blog in model.Blogs)
    {
        if (blog.Kind == "GilFink" && blog.Pinned == "1")
        {
            <tr>
                <td>
                    <h3>
                        <a href="@blog.Url">@blog.Title</a>
                    </h3>
                    @blog.Description
                </td>
            </tr>
        }
    }
    <tbody>
    </tbody>
</table>

<a href="/gil-fink-blog">See more Gil Fink's posts...</a>

<h2>Prashant Brall's Blog</h2>

<table>
    <tbody>
    @foreach (var blog in model.Blogs)
    {
        if (blog.Kind == "PrashantBrall" && blog.Pinned == "1")
        {
            <tr>
                <td>
                    <h3>
                        <a href="@blog.Url">@blog.Title</a>
                    </h3>
                    @blog.Description
                </td>
            </tr>
        }
    }
    </tbody>
</table>

<a href="/prashant-brall-blog">See more Prashant Brall's posts...</a>

<h2>General's Blog</h2>

<table>
    <tbody>
    @foreach (var blog in model.Blogs)
    {
        if (blog.Kind == "General" && blog.Pinned == "1")
        {
            <tr>
                <td>
                    <h3>
                        <a href="@blog.Url">@blog.Title</a>
                    </h3>
                    @blog.Description
                </td>
            </tr>
        }
    }
    </tbody>
</table>

<a href="/general-blog">See more posts...</a>