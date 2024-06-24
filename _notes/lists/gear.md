---
title: Tools
format: list
description: Általam  használt hardverek és szoftverek
permalink: /tools
---

## <i class="fas fa-microchip"></i> Hardware

<ul>
{% for entry in site.data.gear.hardware %}
<li>
    {% if entry.fa_icon %}
    <i class="icon-left {{entry.fa_icon}}"></i>
    {% endif %}
    {% if entry.simple_icon %}
    <img src="https://cdn.simpleicons.org/{{entry.simple_icon}}" class="icon-left">
    {% endif %}
    {{entry.name}}
</li>
{% endfor %}
</ul>

## <i class="fas fa-code"></i> Software

<ul>
{% for entry in site.data.gear.software %}
<li>
    {% if entry.fa_icon %}
    <i class="icon-left {{entry.fa_icon}}"></i>
    {% endif %}
    {% if entry.simple_icon %}
    <img src="https://cdn.simpleicons.org/{{entry.simple_icon}}" class="icon-left" style="width: 18px;">
    {% endif %}
    {{entry.name}}
</li>
{% endfor %}
</ul>

> <i class="fas fa-info-circle"></i> Egy halom általam használt vagy csak egyszerűen hasznos appot / libet találhatsz még a Github profilomon: <a href="https://github.com/tothlp?tab=stars" alt="Github Stars">https://github.com/tothlp?tab=stars</a>
