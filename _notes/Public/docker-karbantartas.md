---
title: Docker karbantartás
feed: show
date: 23-02-2024
---

Ez a script elvileg azokat az image-eket törli, amik nincsenek jelenleg használatban,  és 2 hónapja nem is voltak. Tesztelve lett, de fenntartásokkal kell kezelni, hogy valójában mit is csinál a háttérben. Az biztos, hogy elég szépen kipucolja az elkanászodott image-eket.

```bash
sudo docker image prune --all --filter until=1440h --force
```