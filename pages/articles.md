---
title: "Články"
layout: default
permalink: "/articles/"
---

# Články

<div>
	{% for post in site.articles %} 
		<ul class="list">
			<li><h3 class= "list"><a class="list" href="{{ post.url }}">{{ post.title }}</a></h3></li>
			<li>{{ post.date | date: "%e. %m. %Y" }}</li>
		</ul>
	{% endfor %}
</div>

