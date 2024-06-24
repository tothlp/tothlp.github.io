---
title: Kotlin - Static konstansok
feed: show
date: 23-02-2024
---

Annotációkhoz, pl `@JsonFormat` jöhet jól, ha nem akarjuk 50x ugyanazt a Stringet bemásolni. Kotlinnál erre lehet használni a companion object-et, ebből fordítási idejű konstans lesz, amit már annotációknál is lehet használni.

```kotlin
class Constants {
    companion object {
        const val DATE_TIME_FORMAT_PATTERN = "yyyy-MM-dd HH:mm:ss"
    }
}
```