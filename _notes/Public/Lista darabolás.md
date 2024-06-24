---
title: Lista darabolás
feed: show
date: 17-06-2024
---

Kotlinban a listák darabolásához, méret korlátozáshoz, vagy hívjuk aminek akarjuk, a `chunked` utasítás használható. 

pl.: a data listát max. 1000 méretű darabokra bontjuk:

```kotlin
val requests: List<List<MyCustomRequest>> = data.chunked(1000).map { MyCustomRequest(it) }
```

Ebből nyilvánvalóan egy lista helyett egy listákat tartalmazó lista lesz.
