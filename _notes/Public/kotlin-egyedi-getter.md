---
title: Kotlin - Egyedi getter
feed: show
date: 23-02-2024
---

A propertykhez lehet egyedi accessorokat definiálni. Ha definiálunk egy egyéni gettert, az minden alkalommal meg lesz hívva, amikor megpróbáljuk elérni a propertyt.

### Miért jó ez?

Így lehet egy számított property-t implementálni.


```kotlin
class Rectangle(val width: Int, val height: Int) {

    val area: Int // property type is optional since it can be inferred from the getter's return type

    get() = this.width * this.height

}
```

A property típusa el is hagyható, ha kitalálható a getterből:

```kotlin
val area get() = this.width * this.height
```