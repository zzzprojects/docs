---
PermaID: 1000149
Name: YouTube Videos
---

# YouTube Videos

@using Z.Websites.Web.Models
@{
    var model = (EntityFrameworkFaq)ViewBag.EntityFrameworkFaq;
}

<h2>YouTube</h2>
<table>
    <thead>
        <tr>
            <th>Videos</th>
            <th>Description</th>
        </tr>
    </thead>
    <tbody>
        @foreach (var video in model.Videos) 
        {
            if (video.Kind == "YouTube")
            {
            <tr>
                <td>
                    <iframe width="560" height="315" src="https://www.youtube.com/embed/video.ID" frameborder="0" allow="autoplay; encrypted-media" allowfullscreen></iframe>
                </td>
                <td>
                    <h3>@video.Title</h3>
                    @video.Description
                </td>
            </tr>
            }
        }
    </tbody>
</table>