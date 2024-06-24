---
title: Semaphorok használata, többszálúsítás
feed: show
date: 28-05-2024
---

## Ha az eredményt "be kell várni":

```kotlin
val semaphore = Semaphore(4)
val resp = runBlocking {
	items.map { item ->
		async(context = Dispatchers.IO) {
			semaphore.withPermit {
				// Map item here and return, or...
				val response = runCatching {
					// do something
				}.onFailure {
					// log error
				}.getOrNull()
				// ...return response here, if additional processing is needed
			}
		}
	}.awaitAll()
}
```

* `val semaphore = Semaphore(4)`: Egy `Semaphore` jön létre 4 permittel Ezzel lehet szabályozni, hogy egyszerre hány coroutine futhat párhuzamosan.

* `val resp = runBlocking { ... }`: A `runBlocking` egy coroutine builder, ami blokkolja az aktuális szálat, amíg a blokkban lévő összes coroutine le nem fut. Ennek az eredményéből építjük a `resp` változót. Nyilván, nem muszáj változót létrehozni, ha nem szükséges az eredmény.

* `items.map { item -> ... }`: Az `async` függvénnyel egy új coroutine indul minden item-re. Az `async` függvény egy `Deferred` objektumot ad vissza, ami egy Promise-t reprezentál egy későbbi eredményre.

* `async(context = Dispatchers.IO) { ... }`: Az `async` fgv. a coroutine-t a  `Dispatchers.IO` context-en futtatja. Ez a context az I/O műveletekhez lett optimalizálva, mint például a hálózati kérések, file kezelés.

* `semaphore.withPermit { ... }`: Egy permit-et kér a `Semaphore`-től, és csak akkor fut tovább a kód, ha van szabad permit. Ha nincs, akkor blokkolódik, amíg nem szabadul fel egy permit. A permit-et a blokk végén automatikusan felszabadítja.

* `val response = runCatching { ... }`: A `runCatching` fgv-t a hibakezelésre használjuk. A blokkban lévő kódot futtatja, és ha hiba történik, akkor a hibát visszaadja, egyébként az eredményt. A hibát a `onFailure` blokkban lehet kezelni. Nem muszáj ennek a használata, lehet sima try-catch-et használni, vagy egyáltalán nem kezelni a hibát, csak simán visszaadni az eredményt.


* `.awaitAll()`: Ez a függvény blokkolja a szálat, amíg az összes `Deferred` objektum le nem fut. Az eredmény egy lista lesz az összes `Deferred` objektum eredményéből.

## Ha az eredményt nem kell "bevárni":

```kotlin
CoroutineScope(Dispatchers.IO).launch {
    semaphore.withPermit {
        sendEmails()
    }
}
```

Ez nyilván egy lecsupaszított verzió, nincs hibakezelés. A futása igazából teljesen hasonló az előzőhöz, permitekkel szabályozzuk a párhuzamos futást, aztán valamikor végez. Ha pl. egy email queue-t építünk, ott nem feltétlenül kell várni az eredményre, simán futhat a háttérben. Csak az számít, hogy lefusson.