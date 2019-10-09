---
PermaID: 1000150
Name: Videos
---

# Videos

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
	@foreach( var video in model.Videos )
    { 
        if(video.Kind == "YouTube" && video.Pinned == "1")
         {
		    <tr>
			    <td>
				    <iframe width="560" height="315" src="https://www.youtube.com/embed/@video.ID" frameborder="0" allow="autoplay; encrypted-media" allowfullscreen></iframe>
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


<a href="/youtube-videos">more videos...</a>
=======
<ul>
@foreach( var video in model.Videos )
{ 
    if(video.Kind == "YouTube" && video.Pinned != "1")
    {
		<li>
            <a href="https://www.youtube.com/watch?v=@video.ID" target="_blank">@video.Title</a>
		</li>
    }
}
</ul>

<h2>Lynda</h2>
<table>
	<thead>
		<tr>
			<th>Videos</th>
			<th>Description</th>
		</tr>
	</thead>
	<tbody>
@foreach( var video in model.Videos )
{ 
	if(video.Kind == "Lynda" && video.Pinned == "1") 
	{
		<tr>
			<td>
				<iframe width="560" height="315" src='https://www.lynda.com/player/embed/@video.ID?fs=3&w=560&h=315&ps=paused&utm_medium=referral&utm_source=embed+video&utm_campaign=ldc-website&utm_content=vid-@video.ID' mozallowfullscreen='true' webkitallowfullscreen='true' allowfullscreen='true' frameborder='0'></iframe>
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


<a href="/lynda-videos">more videos...</a> 
=======
<ul>
    @foreach( var video in model.Videos )
    { 
        if(video.Kind == "Lynda" && video.Pinned != "1")
        {
        <li>
            <a href="@video.Url" target="_blank">@video.Title</a> 
        </li>
        }
    }
</ul>

<h3>Channel 9</h3>
<table>
	<thead>
		<tr>
			<th>Videos</th>
			<th>Description</th>
		</tr>
	</thead>
    <tbody>
@foreach( var video in model.Videos )
{ 
    if(video.Kind == "Channel9" && video.Pinned == "1") 
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

<a href="/channel9-videos">more videos...</a>
=======
<ul>
    @foreach (var video in model.Videos)
    {
        if (video.Kind == "Channel9" && video.Pinned != "1")
        {
            <li>
                <a href="https://channel9.msdn.com/@video.ID?ocid=player" target="_blank">@video.Title</a>
            </li>
        }
    }
</ul>


<h3>Pluralsight</h3>
<ul>
	<li><a href="https://app.pluralsight.com/library/courses/entity-framework-6-getting-started/table-of-contents" target="_blank">Getting Started with Entity Framework 6</a></li>
    <li><a href="https://app.pluralsight.com/library/courses/entity-framework-core-getting-started/table-of-contents" target="_blank">Entity Framework Core: Getting Started</a></li>
    <li><a href="https://app.pluralsight.com/library/courses/play-by-play-ef-core-1-0-first-look-julie-lerman/table-of-contents" target="_blank">EF Core 1.0: First Look</a></li>
    <li><a href="https://app.pluralsight.com/library/courses/code-first-entity-framework-legacy-databases/table-of-contents" target="_blank">Code-first Entity Framework with Legacy Databases</a></li>
    <li><a href="https://app.pluralsight.com/library/courses/entity-framework-enterprise-update/table-of-contents" target="_blank">Entity Framework in the Enterprise</a></li>
    <li><a href="https://app.pluralsight.com/library/courses/entity-framework-7-looking-ahead/table-of-contents" target="_blank">Looking Ahead to Entity Framework 7</a></li>
    <li><a href="https://app.pluralsight.com/library/courses/efmigrations/table-of-contents" target="_blank">Entity Framework Code First Migrations</a></li>
    <li><a href="https://app.pluralsight.com/library/courses/efintro-models/table-of-contents" target="_blank">Entity Framework and Data Models</a></li>
    <li><a href="https://app.pluralsight.com/library/courses/querying-entity-framework/table-of-contents" target="_blank">Querying the Entity Framework</a></li>
</ul>
<h3>Udemy</h3>	
<ul>
	<li><a href="https://www.udemy.com/entity-framework-a-comprehensive-course/" target="_blank">Entity Framework : A Comprehensive Course</a></li>
    <li><a href="https://www.udemy.com/entity-framework-tutorial/" target="_blank">Entity Framework in Depth: The Complete Guide</a></li>
    <li><a href="https://www.udemy.com/learn-entity-framework-core-2-efc2-using-aspnet-core/" target="_blank">Learn Entity Framework Core 2.0 (EFC2) using ASP.Net Core</a></li>
    <li><a href="https://www.udemy.com/aspnet-mvc-with-entity-framework-from-scratch/" target="_blank">Asp.Net MVC With Entity Framework From Scratch</a></li>
    <li><a href="https://www.udemy.com/mastering-entity-framework-core-mapping-manipulating-data/" target="_blank">Mastering Entity Framework Core: Mapping & Manipulating Data</a></li>
    <li><a href="https://www.udemy.com/learn_aspnet_bootstrap_entityframework/" target="_blank">Learn ASP NET with Bootstrap,Entity Framework,JavaScript,C#</a></li>
    <li><a href="https://www.udemy.com/learn-aspnet-core-mvc-web-apis-ef-core-bonus-ios-app/" target="_blank">Learn ASP.NET Core using MVC 6 and Entity Framework Core 1.0</a></li>
    <li><a href="https://www.udemy.com/learning-path-mastering-entity-framework-core/" target="_blank">Learning Path: Mastering Entity Framework Core</a></li>
    <li><a href="https://www.udemy.com/learning-entity-framework-core/" target="_blank">Learning Entity Framework Core</a></li>
    <li><a href="https://www.udemy.com/mastering-entity-framework-core-migrations-testing/" target="_blank">Mastering Entity Framework Core - Migrations & Testing</a></li>
</ul>
<h3>Microsoft Virtual Academy</h3>
<ul>
    <li><a href="https://mva.microsoft.com/en-US/training-courses/implementing-entity-framework-with-mvc-8931?l=e2H2lDC3_8304984382" target="_blank">Implementing Entity Framework with MVC</a></li>
</ul>
<h3>Others</h3>
<ul>
	<li><a href="https://codewithmosh.teachable.com/p/entity-framework/?coupon_code=HALFOFF" target="_blank">Entity Framework 6 in Depth</a></li>
	<li><a href="https://www.wintellectnow.com/Videos/Watch?videoId=code-first-development-with-entity-framework" target="_blank">Code-First Development with Entity Framework</a></li>
</ul>