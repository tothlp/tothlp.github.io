---
title: JPA - Modelszintű adattípus konverzió
feed: show
date: 23-02-2024
---

##  Probléma
Sajnos előfordulhat, hogy egy régebbi rendszernél boolean helyett string van használva. Ezeket vagy kézzel minden alkalommal konvertáljuk egy extension function-nel, pl.:

```kotlin
fun String?.toBoolean(): Boolean = this?.equals("i", true) ?: false
fun Boolean?.toOracleString(): String = this?.let { if (it) "I" else "N" } ?: "N"
```

Vagy: egy Attribute Convertert kell írni, ami elvégzi helyettünk. 

[https://thorben-janssen.com/jpa-attribute-converter/](https://thorben-janssen.com/jpa-attribute-converter/)

```kotlin
import jakarta.persistence.AttributeConverter
import jakarta.persistence.Converter

@Converter(autoApply = true)
class BooleanConverter : AttributeConverter<Boolean, String?> {
    override fun convertToDatabaseColumn(attribute: Boolean): String = attribute.toOracleString()
    override fun convertToEntityAttribute(dbData: String?): Boolean = dbData.toBoolean()
}
```

Ha beállítjuk az autoApply-t akkor automatikusan használható, különben kell a `@Convert` annotáció.

```kotlin
@Convert(converter = BooleanConverter::class)
val valid: Boolean,
```

Ez csak a JPA-s műveleteknél használható, tehát pl. oracle eljárás hívásnál nem fog automata konverziót végezni.