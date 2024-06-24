---
title: Biztonságos session sütik
permalink: /biztonsagos-session-sutik
feed: show
date: 01-03-2024
---

## Probléma

By default, a session sütin a Security flag nincs beállítva, így nyílt szövegben kerül átvitelre a sessionID még a HTTPS kapcsolat kiépítése előtt. Beállítása (részben) kiküszöböli a Session Hijacking támadásokat. 

## Megoldás

Az `application.yml`-ben fel kell venni a következőt:

```yml
server:
  servlet:
    session:
      cookie:
        secure: true
```

Ez viszont csak akkor legyen beállítva, ha vagy az alkalmazás SSL-t használ, vagy van előtte egy [[Reverse Proxy]]. Ha nem így teszünk, akkor localhoston működni fog a belépés, viszont külső elérésnél nem lesz érvényes a süti.