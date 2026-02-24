---
title: "Básně"
layout: default
permalink: "/poetry/"
---

# Básně

<ul>
{% for poem in site.poems %}
    <li><a href="{{- poem.redirect_to -}}">{{ poem.title }}</a></li>
{% endfor %}
</ul>
