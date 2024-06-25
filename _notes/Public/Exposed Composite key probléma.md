---
title: Exposed Composite key probléma
feed: show
date: 25-06-2024
slug: exposed-composite-key-problema
---

Bármikor lehetünk olyan szerencsétlenek, hogy egy olyan adatbázis táblával találkozunk, aminek nem egy, hanem több mezőből álló,  összetett (composite) elsődleges kulcsa van.

Sajnos az Exposed ennek kezelésére csak félmegoldást kínál.

Adott a következő entitás:

```kotlin
object Cats : Table("Cats") {
	val name = varchar("name", 50)
	val breed = varchar("breed", 20)
	override val primaryKey = PrimaryKey(arrayOf(name, breed), name = "compositeKey")
}
```

Itt ugye simán létre tudtuk hozni az elsődleges kulcsot a `PrimaryKey` használatával és nagyon egyszerűen meg tudtuk adni, hogy mely mezőkből álljon az összetett kulcs. A varázslatnak itt vége. A következő lépés ugye általában az lenne, hogy a DSL után létrehozzuk a DAO objektumunkat, ahol 	egyrészt meg kell adni az ID típusát, másrészt egy `EntityClass` típusú companion object létrehozásával kell folytatni. 

```kotlin
companion object : LongEntityClass<Cat>(Cats)
```

A rossz hír, hogy az `EntityClass` csak egy mezőt tud kezelni elsődleges kulcsként, így az összetett kulcsokkal nem tud mit kezdeni. Következésképpen, az ilyen jellegű táblákhoz (egyelőre) nem tudunk DAO-t létrehozni. Ez sajnos a lekérdezési lehetőségeinket is korlátozza.

> Nyitott issue: [https://github.com/JetBrains/Exposed/issues/43](https://github.com/JetBrains/Exposed/issues/43)