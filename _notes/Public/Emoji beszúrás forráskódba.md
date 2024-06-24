---
title: Emoji beszúrás forráskódba
feed: show
date: 24-02-2024
permalink: /emoji-beszuras-forraskodba
---

Ma sikerült rájönnöm, miközben szépítgettem a landinget, hogy az emojik ma már nem csak képecskék, hanem unicode karakterek. Éppen azzal szenvedtem, 
hogy keresgéltem a Github cheatsheet-ben egy emoji kódját, miközben megláttam, hogy be van szúrva a configba egy emoji. A macOS emoji billentyűzetéről 
beszúrtam egy másikat, ami a vscode-ban nem volt valami szép csicsás, de kirajzolta. Ment a publish, és simán kirenderelte, ráadásul a source-t se 
szemetelte tele. 22. század... Mindenesetre rendes kódban nem szeretném viszont látni...Nem tudom, hogy a Kotlin megeszi-e, de a :

```kotlin
fun 😎() = println("YOLO!")
```

azért elég ocsmány...