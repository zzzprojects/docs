---
Name: LINQ Dynamic
---

# LINQ Dynamic

## LINQ Dynamic

<ul>
{% for num in (0..site.data.pages.size) %}	
	{% if site.data.pages[num].category == 'linq' and site.data.pages[num].url != page.permalink %}
		<li><a href="{{ site.data.pages[num].url }}">{{ site.data.pages[num].title }}</a></li>
	{% endif %}
{% endfor %}
</ul>
