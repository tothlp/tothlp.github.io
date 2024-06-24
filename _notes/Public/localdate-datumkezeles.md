---
title: Spring - LocalDate dátumkezelés
feed: show
date: 23-02-2024
---

# Tünet

Kérések feldolgozásakor a String-et nem lehet parsolni. Lsd:

`Failed to convert property value of type 'java.lang.String' to required
type 'java.time.LocalDate' for property..`

# Megoldás

A POST kéréseknél megoldható a probléma, ha a request-ben az adott paramétert annotáljuk a
következővel:

```java
@JsonFormat(shape=JsonFormat.Shape.STRING, pattern="yyyy.MM.dd hh:mm:ss")
```

Megj.: Néhány esetben (pl. XML modeleknél) magát a field-et kell annotálni, ilyenkor `@field:JsonFormat` … annotációt kell használni.

**DE!**

GET kéréseknél nem működik ez a módszer. Egy pár property-vel globálisan
megoldható a kezelésük:

```yaml
spring:
  mvc.format:
    date: yyyy-MM-dd
    date-time: yyyy-MM-dd HH:mm:ss
    time: HH:mm:ss
```

A response-okra sajnos még kell annotáció, ha egyedi formátumot
akarunk.

Van több esetleges megoldás is, azokat még nem ellenőriztem mindet: [https://www.baeldung.com/spring-date-parameters](https://www.baeldung.com/spring-date-parameters)