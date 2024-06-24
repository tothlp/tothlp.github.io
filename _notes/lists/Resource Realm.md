---
title: Resource Realm
description: Cikkek és egyéb érdekes / hasznos resource-ok
permalink: /resource-realm
format: list
---

{% for article in site.data.resources %}
* <a href="{{ article.url }}" target="_blank">{{ article.title }}</a> {% if article.description %}- {{ article.description }} {% endif%}
{% endfor %}
