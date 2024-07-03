---
title: Spring active profile
feed: show
date: 03-07-2024
---

Spring(boot) alkalmazásoknál az aktív profilt beállíthatjuk a következőképpen:

- `application.properties` vagy `application.yml` fájlban a `spring.profiles.active` property értékének beállításával (ez szerintem nem ajánlott)
- IntelliJ IDEA Ultimate esetén a Run Configuration-nél a `Active profiles` mezőben.
- IntelliJ IDEA Community esetén a Run Configuration-nél :
  - a `VM options` mezőben a `-Dspring.profiles.active=development`
  - vagy az `Environment variables` mezőben a `SPRING_PROFILES_ACTIVE=development` értékkel.

Ha minden alkalmazásnál egy adott profilt szeretnénk beállítani, akkor megtehetjük, hogy felveszünk egy globális környezeti változót, `SPRING_PROFILES_ACTIVE` néven, és az értékének beállítjuk a kívánt profilt. Ha ezt a módot választjuk, újra kell indítani az IntelliJ-t, hogy a változások érvénybe lépjenek.