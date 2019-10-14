---
PermaID: 1000133
Name: Books
---

# Books

@using Z.Websites.Web.Models
@{
    var model = (EntityFrameworkFaq)ViewBag.EntityFrameworkFaq;

}

<h2>Amazon</h2>

<table>
	<thead>
		<tr>
			<th>Book</th>
			<th>Description</th>
		</tr>
	</thead>
	<tbody>
	@foreach( var book in model.Books)
    {
        if(book.Store == "Amazon")
        { 
		<tr>
			<td>
				<iframe style="width:120px;height:240px;" marginwidth="0" marginheight="0" scrolling="no" frameborder="0" src="//ws-na.amazon-adsystem.com/widgets/q?ServiceVersion=20070822&OneJS=1&Operation=GetAdHtml&MarketPlace=US&source=ac&ref=qf_sp_asin_til&ad_type=product_link&tracking_id=zzzprojects-20&marketplace=amazon&region=US&placement=@book.ID&asins=@book.ID&linkId=b2d26d428e2e7febce718b43cdb7e867&show_border=true&link_opens_in_new_window=true&price_color=333333&title_color=cc0000&bg_color=ffffff"></iframe>
			</td>
			<td>
				<h3>@book.Title</h3>
				@book.Description
			</td>
		</tr>
        }
    } 		
	</tbody>
</table>

<h2>Google Books</h2>

<table>
	<thead>
		<tr>
			<th>Book Preview</th>
			<th>Description</th>
		</tr>
	</thead>
    <tbody>
        @foreach (var book in model.Books)
        {
            if (book.Store == "GoogleBooks")
            {
            <tr>
                <td>
                    <iframe frameborder="0" scrolling="no" style="border:0px" src="https://books.google.com.pk/books?id=@book.ID&lpg=PP1&pg=PP1&output=embed" width="500" height="500"></iframe>
                </td>
                <td>
                    <h3>@book.Title</h3>
                    @book.Description
                </td>
            </tr>
            }
        }
    </tbody>
</table>