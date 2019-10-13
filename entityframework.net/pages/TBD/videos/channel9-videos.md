---
PermaID: 1000152
Name: Channel9 Videos
---

# Channel9 Videos

@using Z.Websites.Web.Models
@{
    var model = (EntityFrameworkFaq)ViewBag.EntityFrameworkFaq;
}

<h2>Channel 9</h2>
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
        if (video.Kind == "Channel9")
        {
        <tr>
            <td>
                <iframe width='560' height='315' src="https://channel9.msdn.com/@video.ID/player?format=smooth" mozallowfullscreen='true' webkitallowfullscreen='true' allowFullScreen frameBorder="0"></iframe>
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