---
title: Experience
content-type: static
description: 
permalink: /xp
layout: Post
---
## Skills

{% for skill in site.data.skills %}
<b> {{ skill.category }} </b>

<div class="skills">
{% for item in skill.items %}
<img src="{{item.icon}}">
{% endfor %}

</div>

{% endfor %}

## Tapasztalat

{% for entry in site.data.xp.work %}
<div class="flex-container">
    <div class="data">
        <p class="maintitle">{{entry.company}}</p>
        <p class="subtitle">{{entry.role}}</p>
        <p class="other">{{entry.period}}</p>
        {% if entry.link %}
        <p class="other">
            <a href="{{entry.link}}">{{entry.link}}</a>
        </p>
        {% endif %}
    </div>
    <div class="details">
        <ul>
            {% for task in entry.description %}
            <li>{{task}}</li>
            {% endfor %}
        </ul>
    </div>
</div>
{% endfor %}

## Tanulmányok

{% for entry in site.data.xp.education %}
<div class="flex-container">
    <div class="data">
        <p class="maintitle">{{entry.institute}}</p>
        <p class="subtitle">{{entry.program}}</p>
        <p class="other">{{entry.period}}</p>
    </div>
    <div class="details">
        <ul>
            {% for task in entry.description %}
            <li>{{task}}</li>
            {% endfor %}
        </ul>
    </div>
</div>
{% endfor %}