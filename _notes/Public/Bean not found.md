---
title: Bean not found
feed: show
date: 19-03-2024
---

Ha találkozunk a ".... Bean that could not be found" hibával, viszont mi nagyon létrehoztuk már a Bean-t, akkor vszg az lehet a baj, hogy a Spring egyszerűen nem találja meg. Nyilván, egy mezei üres fájlban, vagy osztályban nem is fogja, ezért kell egy `@Configuration` annotációval ellátni a konfigurációs osztályt, amiben a Bean van.

```kotlin
@Configuration
fun MyConfig {
    @Bean
    fun myBean(): MyBean = MyBean()
}
```
