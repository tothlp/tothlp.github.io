---
title: About
content-type: static
description: "Utoljára frissítve: 2025. 03. 11."
permalink: /about
layout: Post
---


<div class="about-container">
<div class="profile-img" style="background-image: url({{ site.about_profile_image | relative_url }})"></div>
<div class="about-text">
Több, mint 7 éve foglalkozom felhasználóbarát és jól karbantartható Java és Kotlin alapú Spring Boot alkalmazások fejlesztésével.
Szeretek új technológiákkal megismerkedni, majd ezeket a mindennapi munkámban hasznosítani és másokkal megosztani.

Rendszeresen olvasok tech cikkeket és changelogokat, hogy naprakész legyek a legújabb fejlesztésekkel kapcsolatban. 🤓
Gyakran használok angol kifejezéseket a mindennapi beszédemben, és utálom a kényszerfordított zsargont.
<br><br>

Szeretek megúszós és finom kajákat főzni. 👨‍🍳 
Az erdei és hegyi túrák alatt érzem úgy igazán, hogy töltőn vagyok, habár sajnos ritkán van erre lehetőségem. 
A hétköznapokban a jóga is segít. 🏞️ 🌲
Kávéfüggő vagyok, ☕️ főleg a világos pörkölésű kávét és az alternatív elkészítési módokat szeretem. 
Szabadidőmben szívesen nyösztetek videójátékokat, szigorúan konzolon. 🎮 Néha fényképezek. 📷
</div>
</div>

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
