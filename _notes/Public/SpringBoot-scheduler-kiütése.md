---
title: SpringBoot Scheduler kiütése
slug: spring-scheduler-kiutese
feed: show
date: 30-07-2024
---

SpringBoot alkalmazásoknál előfordulhat, hogy egy adott `@Scheduled` annotációval ellátott folyamatnál nem szeretnénk, hogy bármikor fusson. Ennek megoldására több lehetőség is van, az egyik a `@ConditionalOnProperty` annotáció használata, a másik pedig a `@Scheduled` annotáció `cron` paraméterének használata.

Utóbbi (és vszg. legegyszerűbb megoldás), ha a cron paraméterrel kapcsoljuk ki a futást.
`application.yml`-ben pl.:
```yaml
app:
  my-scheduler:
    cron: '-'
```

A `-` érték segítségével a cron paramétert ki tudjuk kapcsolni, így a folyamat nem fog lefutni.