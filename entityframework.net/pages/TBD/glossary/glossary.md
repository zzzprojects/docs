---
PermaID: 1000029
Name: Glossary
---

# Glossary

@using Z.Websites.Web.Models
@{
    var model = (EntityFrameworkFaq)ViewBag.EntityFrameworkFaq;

}

@{
    string glossary_category = "defaut";
    bool begin = true;
}
@foreach (var item in model.Glossaries)
{
    if (@item.Category != glossary_category && !begin)
    {
        @Html.Raw("</table>");
    }

    if (@item.Category != glossary_category)
    {
        begin = false;
        glossary_category = item.Category;
        @Html.Raw("<h2 id=" + item.Category + ">" + item.Category + "</h2>");
        @Html.Raw("<table>")
        <thead>
            <tr>
                <th>Term</th>
                <th>Definition</th>
            </tr>
        </thead>
    }
    <tr>
        <td>@item.Term</td>
        <td>@item.Definition</td>
    </tr>
}

@Html.Raw("</table>")