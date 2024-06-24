---
title: Png használata faviconként
feed: show
date: 25-02-2024
permalink: /png-hasznalata-faviconkent
---

Az [[Emoji beszúrás forráskódba]] margójára. A napokban elkezdtem használni a Github Copilot-ot és szégyen gyalázat, nem jutott eszembe, hogy pontosan hogy lehet HTML-ben favicont beszúrni.
Legnagyobb meglepetésemre, ma már lehetséges `.png` fájlokat is használni erre a célra. Nem bánom, mert az `.ico` fájlokat semmi másra nem tudtam használni, szóval így csak erre a célra lehetett 
pluszba konvertálgatni.

```html
<link rel="icon" href="{{ site.baseurl }}/assets/img/favicon.png" type="image/png" sizes="16x16" />
```