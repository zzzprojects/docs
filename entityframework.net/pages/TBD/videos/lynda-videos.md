---
PermaID: 1000151
Name: Lynda Videos
---

# Lynda Videos

@using Z.Websites.Web.Models
@{
    var model = (EntityFrameworkFaq)ViewBag.EntityFrameworkFaq;
}

<h2>Lynda </h2>
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
            if (video.Kind == "Lynda")
            {
                <tr>
                    <td>
                        <iframe width='560' height='315' src='https://www.lynda.com/player/embed/@video.ID?fs=3&w=560&h=315&ps=paused&utm_medium=referral&utm_source=embed+video&utm_campaign=ldc-website&utm_content=vid-@video.ID' mozallowfullscreen='true' webkitallowfullscreen='true' allowfullscreen='true' frameborder='0'></iframe>
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