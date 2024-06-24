---
title: OpenAPI Springdoc Setup
feed: show
date: 28-03-2024
---

Doksi: [https://springdoc.org](https://springdoc.org)

Java17-tel, Springboot 3-mal lett a következő megoldás kipróbálva. A használathoz szükséges minimum dependencia:


```kotlin
implementation("org.springdoc:springdoc-openapi-starter-webmvc-ui:2.1.0")
```

Ha kicsit fel akarjuk turbózni, be kell húzni a common-t is:
```kotlin
  implementation("org.springdoc:springdoc-openapi-starter-common:2.1.0")
```

Ez azért jó, mert elősegíti a Kotlin használatát, pl. támogatott a [[Kotlin - DTO validálás]] validációs annotációk segítségével a dokumentáció kibővítése. (lsd. min-max feltételek.)

## Separating docs from logic

2024.04.01.: Simán meg lehet csinálni, hogy a RESTful interface-t toljuk tele annotációkkal. Ez egyrészt elősegíti, hogy már akkor tudunk dokumentálni, meg contract-ot írni amikor még semmit nem álmodtunk meg az implementációról, másrészt így a Controllerben lévő "üzleti logika" és a dokumentáció külön lesz kezelve. A Springdoc simán meg tudja emészteni a `@GetMapping`, `@RequestMapping`, `@Operation`, `@ApiResponse`, stb annotációkat, viszont a `@RestController` annotációnak muszáj implementációra kerülnie, szóval egy implementációs osztályt muszáj létrehozni.

Annyiban lehet még ejtőzni, hogy az overrideolt metódusok body-ja simán lehet `TODO()`.

## Konfigurációk

lsd.: [https://springdoc.org/#properties](https://springdoc.org/#properties)

```yaml
springdoc:
    default-produces-media-type: application/json
    override-with-generic-response: false
```

* default-produces-media-type: alapértelmezett media-type a response-okhoz
* override-with-generic-responses: ha be van kapcsolva, akkor minden végponthoz generál (használattól függően) 403-mas, 404-es, stb. státuszt. jobb kinyomni.
