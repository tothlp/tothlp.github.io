---
title: Gradle - Új modul hozzáadása
feed: show
date: 26-02-2024
permalink: /gradle-uj-modul-hozzaadasa
---

Ha nem az IDE segítéségével hozzuk létre a modult, akkor a mappaszerkezet kialakítása után a következőket kell tenni:

A projekt gyökerében található `settings.gradle`-ben:

```gradle
    include 'modules/new-module'
    project(":modules/new-module").name = "NewModule"
```

Illetve a projekt gyökerében található `build.gradle.kts`-ben:

```gradle
	implementation(project(':NewModule'))
```