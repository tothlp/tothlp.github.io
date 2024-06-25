---
title: Exposed Joins
feed: show
date: 25-06-2024
---


Az [Exposed](https://github.com/JetBrains/Exposed) is természetesen támogatja a join-ok használatát, de nem minden esetben olyan egyszerű, mint a JPA esetében. 

## Alapozás

Először az Entityket hozzuk létre. Semmi extra, one-to-many kapcsolat Emberek és Macskák között, ahol egy embernek több macskája is lehet.
Remény szerint rengeteg.

```kotlin
object Cats : LongIdTable("Cat") {
	val owner = reference("owner", Persons)
	val name = varchar("name", length = 50)
	val breed = varchar("breed", length = 50)
}

class Cat(id: EntityID<Long>) : LongEntity(id) {
	companion object : LongEntityClass<Cat>(Cats)

	val owner by Person referencedOn Cats.owner
	var name by Cats.name
	var breed by Cats.breed
}

object Persons : LongIdTable("Person") {
	val name = varchar("name", length = 50)
}

class Person(id: EntityID<Long>) : LongEntity(id) {
	companion object : LongEntityClass<Person>(Persons)

	var name by Persons.name
	val cats by Cat referrersOn Cats.owner
}
```

Itt érdemes megnézni a `cats` változót. Látjuk, hogy ez csak a DAO-ban van jelen, tehát nem tartozik hozzá konkrét adatbázis mező. A referrersOn miatt viszont "visszahivatkozik" a Cat táblára, és ezen segítségével (is) tudjuk lekérni az adott személyhez tartozó macskákat. 

Hogy valamit kezdjünk is az adatokkal, létrehozunk egy pár faék egyszerűségű dummy metódust "responseok" gyártásához:
*(Nem csináltam egyedi struktúrát, nyilván, bármibe lehetne mappelni, amit nem szégyellsz...)*

```kotlin
fun Person.toResponse() = mapOf(
	"id" to id.value,
	"name" to name,
	"cats" to cats.map { cat -> cat.toResponse() }
)

fun Cat.toResponse() = mapOf(
	"id" to id.value,
	"name" to name,
	"breed" to breed
)
```

## DAO lekérés

```kotlin
Persons.selectAll().map { person -> Person.wrapRow(person).toResponse() }
```

Itt a DSL segítségével lekérjük az összes személyt, ami "magával rántja" a hozzájuk tartozó macskákat is. A `map` segítségével pedig a Person objektumokat átalakítjuk a responseokká.

**Probléma:**

Egyrészt, ez a megközelítés csak akkor használható, ha van DAO objektumunk (lsd: cats változó). Másrészt, sajnos itt az Exposed nem annyira bölcs, hogy join-t használjon, így minden egyes Person objektumhoz külön lekérdezést fog küldeni a macskákért. Így egy csodás **N+1** query problémát kaptunk. Nyilván, kis adatmennyiség esetén nem fogja megfektetni a rendszerünket, de semmiképpen sem nevezném optimálisnak.

Érdemes megjegyezni, hogy ezt a megközelítést pl. nem fogjuk tudni használni olyan tábláknál, amik összetett elsődleges kulccsal rendelkeznek. *Lásd: [[Exposed Composite key probléma]]*

## Lekérés InnerJoin-nal

Ennél a megoldásnál megírjuk a join-t, majd a map segítségével alakítjuk át a kapott adatokat. Sajnos a JPA-tól eltérően, az Exposed nem tud "ORM-ként", objektum szinten gondolkodni, tehát hiába kérem le a személyeket és a macskákat, nem fogja tudni a személyekhez alábontani a macska listát. Az Exposed ResultRow-ban gondolkodik, tehát teljesen hasonló a helyzet, minta kézzel kiadnánk a selectünket, és a lejött adatokból kézzel hoznánk létre a szükséges sturktúrát.

```kotlin
(Persons innerJoin Cats)
	.selectAll()
	.groupBy { it[Persons.id] }
	.map { group ->
		val cats = group.value.map { Cat.wrapRow(it) }

		Person.wrapRow(group.value.first()).toResponse(cats)
	}
```

Annyi segítséget kapunk, hogy a `wrapRow` segítségével a ResultRow-t átalakíthatjuk a megfelelő DAO objektummá, nem kell egyesével beállítani a mezőket. Mivel azt szeretnénk, hogy a Person objektumokhoz tartozó Cat objektumok listaként alá legyenek bontva, így szükséges egy groupingot alkalmazni. A join-os megoldás használatakor nem elég a joint lefuttatni, a kapott adatokat is wrapelni kell, ezért a Cat objektumot is wrapRow-val hozzuk létre.

Azt nyilvánvalóan nekünk kell eldöntenünk, hogy milyen joint alkalmazunk. Ezzel a megoldással egyelőre nincs probléma. A bonyodalom ezután jön.

## Lekérés LeftJoin-nal

Ha az előző példát `leftJoin`-ra cseréljük, akkor még mindig nem lesz semmi probléma. Egészen addig, amíg egyszer egy személyhez nem tartozik macska. Ekkor ugyanis a `Cat.wrapRow(it)` hívás közben az `id` mező null lesz, ami a by-design nem megengedett a kulcsoknál. Következésképp egy méretes `NullPointerException`-t kapunk. A problémát ki lehet küszöbölni akkor, ha a `Cat.wrapRow(it)` hívás előtt ellenőrizzük, hogy az `id` mező nem null-e. Elég fájdalmas, hogy ezt kezelgetni kell, de legalább megoldja a problémát.

```kotlin
(Persons leftJoin Cats)
	.selectAll()
	.groupBy { it[Persons.id] }
	.map { group ->
		val cats = group.value.filter { it.getOrNull(Cats.id) != null }.map { Cat.wrapRow(it) }

		Person.wrapRow(group.value.first()).toResponse(cats)
	}
```

Már csak annyi hiányzik, hogy a response létrehozáskor a Cat objektumokat is átadjuk. Ezt a `toResponse` metódust kell módosítani, hogy paraméterként megkapja a macskákat.

```kotlin
fun Person.toResponse(allCats: List<Cat>) = mapOf(
	"id" to id.value,
	"name" to name,
	"cats" to allCats.map { cat -> cat.toResponse() }
)
```