---
title: "Básně"
layout: default
permalink: "/poetry/"
---

# Básně

<ul>
{% for poem in site.poems reversed %}
    {% if poem.layout == "external_link" %}
        <li>
            <a href="{{- poem.redirect_to -}}">{{- poem.title -}}</a>
            ({{- poem.redirect_to
                | remove: "https://"
                | remove: "http://"
                | split: "/"
                | first
            -}})
        </li>
    {% else %}
        <li><a href="{{- poem.url | relative_url -}}">{{- poem.title -}}</a></li>
    {% endif %}
{% endfor %}
</ul>
