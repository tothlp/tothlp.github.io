---
title: Uses
format: list
description: Milyen eszközöket használok?
permalink: /uses
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

## <i class="fa-solid fa-mug-hot"></i><a name="coffee"></a> Coffee

* **Pod Brewer:** Krups Essenza Mini (XN1108CP)
* **Coldbrew:** Hario Mizudashi
* **"Espresso" / Percolation:** Bialetti Moka Express
* **Filter:**  Aeropress (+ Fellow Prismo, optional)
* **Other, non-used brewers:** Nanopresso, French Press
* **Scale:** Timemore Black Mirror Basic
* **Grinder:** 1Zpresso JX
* **Kettle:** Tchibo (98096) elektromos hattyúnyakú vízforraló

> <i class="fas fa-info-circle"></i> More coffee content: [[Coffee]]
