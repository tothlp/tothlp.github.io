---
title: Új Oracle Driver
feed: show
date: 09-06-2023
---

Krisztus előtti rendszereknél még ősrégi Oracle drivereket kellett használni. Manapság ezekkel egyrészt lehet kompatibilitási probléma is, illetve bármikor leszedhetik őket a központi repókból, pont, ahogy én is jártam. Az újabb driverek vszg még hosszú évekig lesznek támogatva, viszont figyelni kell arra, hogy az orai18n libet is behúzzuk, különben nagyon csúnya hibákat tapasztalhatunk, ha magyar karakterekkel merészelünk dolgozni. Ez régebben elvileg bele volt a driverbe csomagolva, de idővel kiszervezték, joggal.

```bash
implementation("com.oracle.database.jdbc:ojdbc8:21.9.0.0")
implementation("com.oracle.database.nls:orai18n:21.7.0.0")
```