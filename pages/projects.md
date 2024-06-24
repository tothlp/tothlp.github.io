---
title: Projects
content-type: static
description: 
permalink: /projects
layout: Post
---

{% for entry in site.data.projects %}
<div class="flex-container">
    <div class="data">
        <p class="maintitle">
            {% if entry.icon %}
                <i class="icon-left {{entry.icon}}"></i>
            {% endif %}
        {{entry.title}}</p>
        {% if entry.url %}
        <p class="other">
            <a href="{{entry.url}}">{{entry.url}}</a>
        </p>
        {% endif %}
        <div class="additional_icons">
        {% for link in entry.additional_links %}
            <a href="{{link.url}}">
            {% if link.icon %}<i class="icon-left {{link.icon}}"></i>
            {% else %} {{link.title}}
            {% endif %}
            </a>    
        {% endfor %}
        </div>
    </div>
    <div class="details left">
        {{entry.description | markdownify}}
    </div>
</div>
{% endfor %}