---
title: Kotlin - Retry
feed: show
date: 28-05-2024
---

A retry-hoz egy flow-t kell használni, a retry-hoz pedig coroutine környezet kell. Coroutine-hoz, többszálúsításhoz: [[Semaphorok használata, többszálúsítás]]

```kotlin
runBlocking {
	flow {
		// Do something
		val result = doSomething()
		emit(result)
	}.retry(2) {
		// Retry if exception is thrown, log error, etc.
		// set delay
		true.also { delay(2000) }
	}.catch {
		// Handle final error, after retry.
	}.collect {
		// Do something with the result
	
	}
}
```

* `flow { ... }`: Egy `Flow` objektumot hoz létre, ami egy aszinkron adatfolyamot reprezentál. A blokkban lévő kód fut aszinkron módon, és az `emit` függvénnyel lehet értékeket küldeni a folyamba.

* `.retry(2) { ... }`: Ezzel irányítható, hogy a flow-ban megadott kódot hányszor próbáljuk meg újra futtatni. Itt is vissza kell adni egy értéket, ami alapján döntünk, hogy újra próbáljuk-e. Itt a retry számától függetlenül lehet további ellenőrzéseket berakni, tehát ha pl. egy 5-ös retry-unk van, lehet olyan eset, amire már nem akarunk újra próbálkozni, akkor abban a logikában `false`-t kell visszaadnunk. A jelenlegi példában nincs ilyen, szóval `true`-t adunk. 
	* Erre tudunk egy delay-t ráhívni, jelen esetben 2 mp után próbálkozik újra.
* A `.catch { ... }` blokkban lehet kezelni azokat az eseteket, amikor a retry már végleg nem sikerül, tehát a flow-ban megadott kód hibát dob.
* A `.collect { ... }` blokkban lehet feldolgozni a flow-ból érkező értékeket.