---
title: Uses
format: list
description: "Utoljára frissítve: 2025. 03. 12."
permalink: /uses
---

{% for category in site.data.gear %}
<h2>
    {% if category.fa_icon %}
        <i class="icon-left {{category.fa_icon}}"></i>
    {% elsif category.simple_icon %}
        <img src="https://cdn.simpleicons.org/{{category.simple_icon}}" class="icon-left" style="width: 18px;">
    {% elsif category.material %}
        <i class="icon-left material-symbols-outlined">{{category.material}}</i>
    {% endif %}
    {{category.category}}
</h2>
{% if category.info %}
<blockquote>
    <i class="fas fa-info-circle icon-left"></i> {{category.info}}:
    {% if category.info_url %}
        {% capture starts_with_slash %}
          {{ category.info_url | slice: 0 | strip }}
        {% endcapture %}
        {% if starts_with_slash contains '/' %}
            <a href="{{category.info_url | relative_url}}">{{category.info_url}}</a>
        {% else %}
            <a href="{{category.info_url}}"></a>
        {% endif %}
    {% endif %}
</blockquote>
{% endif %}
<ul>
{% for entry in category.items %}
    <li>
        {% if entry.fa_icon %}
            <i class="icon-left {{entry.fa_icon}}"></i>
        {% elsif entry.simple_icon %}
            <img src="https://cdn.simpleicons.org/{{entry.simple_icon}}" class="icon-left" style="width: 18px;">
        {% elsif entry.material %}
            <i class="icon-left material-symbols-outlined">{{entry.material}}</i>
        {% elsif entry.label %}
            <b>{{entry.label}}:</b>
        {% endif %}
        {{entry.name}}
        {% if entry.url %}
         <a href="{{entry.url}}" target="_blank"></a>
        {% endif %}
    </li>
{% endfor %}
</ul>
{% endfor %}

