---
title: Kotlin notebook dependency
feed: show
date: 29-05-2024
---

Elvileg van pár beépített library amit a `%use libraryName` paranccsal lehet behúzni, mint pl. az exposed-ot. 

Ha a MavenCentral-ról akarunk library-t használni akkor a következő parancs használható:

```ipynb
@file:DependsOn("group:artifact:version")
```