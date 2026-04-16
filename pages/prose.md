---
title: "Prose"
layout: default
permalink: "/prose/"
---

# Prose

Short prosaic stories that are non-scientific and mostly fictional.

<ul>
{% for post in site.posts reversed %}
    {% if post.category == "Povídky" %}
        <li><a href="{{- post.url | relative_url -}}">{{- post.title -}}</a></li>
    {% endif %}
{% endfor %}
</ul>
