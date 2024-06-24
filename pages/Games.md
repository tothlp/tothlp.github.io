---
title: Games
content-type: static
description: Egy nem teljes lista azokról a játékokról, amiket toltam az évek során.
permalink: /games
layout: Post
format: list
---

{% assign games_by_year = site.data.games | reverse | group_by:"year" %}
{% for year in games_by_year %}
  <h3> {{ year.name }} </h3>

  <div class="grid-container">
{% for game in year.items %}
  <div class="grid-item" style="background-image: url('{{'/assets/img/covers/' | append: game.cover | relative_url }}');">
    <!-- <img src="{{'/assets/img/covers/' | append: game.cover | relative_url }}" alt="{{game.cover}}"> -->
      <div class="overlay">
          <p class="item-title">{{ game.title }} </p>
          <p class="item-icon">
              {% if game.platform == "PS" %}
                <p title="PlayStation"><i class="fa-brands fa-playstation"></i></p>
              {% elsif game.platform == "PC" %}
                <p title="PC"><i class="fa-solid fa-desktop" alt="PC"></i></p>
              {% elsif game.platform == "XBOX" %}
                <p title="Xbox"><i class="fa-brands fa-xbox"></i></p>
              {% endif %}
              {% if game.platform == "APPLE" %}
                <p title="Apple (iOS, tvOS, stb.)"><i class="fa-brands fa-apple"></i></p>
              {% endif %}
          </p>
          <p class="item-icon">
              {% if game.unfinished %}
                <p title="Nem befejezett"><i class="fa-solid fa-spinner"></i></p>
              {% endif %}
          </p>
      </div>
  </div>    
{% endfor %}
  </div>

{% endfor %}