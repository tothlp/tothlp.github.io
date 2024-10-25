---
title: Minimal JSON serialization setup
feed: show
date: 25-10-2024
---

>Bővebben: [https://github.com/FasterXML/jackson-module-kotlin](https://github.com/FasterXML/jackson-module-kotlin)


Ha egyedi dátumokat / adattípusokat szeretnénk serializálni / deserializálni, vagy nincs behúzva a projektbe a Spring Web, vagy default értékek nélküli osztályokat akarunk használni, pl: 

```kotlin
data class MyData(
    val persons: List<Person>,
)

data class Person(
    val name: String,
    val birthDate: LocalDate,
)
```

akkor serializáláskor különböző hibákat kaphatunk, pl `No serializer found for class java.time.LocalDate` vagy `Cannot construct instance of java.time.LocalDate (no Creators, like default construct, exist): no String-argument constructor/factory method to deserialize from String value ('2024-10-25')`, vagy `Cannot construct instance of MyData (no Creators, like default construct, exist): cannot deserialize from Object value (no delegate- or property-based Creator)`.

Ezek megoldására a legegyszerűbb megoldás, ha behúzzuk a következő dependency-ket:

```kotlin
implementation("com.fasterxml.jackson.module:jackson-module-kotlin")
implementation("com.fasterxml.jackson.datatype:jackson-datatype-jsr310")
implementation("com.fasterxml.jackson.datatype:jackson-datatype-jdk8")
```

Fontos, hogy az ObjectMapper Bean-ben be legyenek a megfelelő modulok regisztrálva:

```kotlin

@Configuration
class JacksonConfig {

    @Bean
    fun objectMapper(): ObjectMapper {
        JsonMapper.builder()
            .registerModule(JavaTimeModule())
            .registerModule(Jdk8Module())
            .build()
    }
}
```

Ha nem akarunk egyesével modulokat regisztrálni, akkor a findAndAddModules() metódus segítségével is megoldható:

```kotlin
@Configuration

class JacksonConfig {

    @Bean
    fun objectMapper(): ObjectMapper {
        JsonMapper.builder().findAndAddModules().build()
    }
}
```

> A két megoldás közül CSAK az egyik használható. Ha hagyatkozunk az automatikus és kézi beállításra is, akkor csak az egyik fog érvényesülni.

Fontos, hogy mindneképp regisztráljuk be a KotlinModule-t is:

```kotlin
object JacksonConfig {

    @Bean
    fun objectMapper(): ObjectMapper {
        JsonMapper.builder().findAndAddModules()    
        .disable(SerializationFeature.WRITE_DATES_AS_TIMESTAMPS, SerializationFeature.WRITE_DATES_WITH_ZONE_ID)
        .enable(MapperFeature.ACCEPT_CASE_INSENSITIVE_PROPERTIES).enable(JsonParser.Feature.ALLOW_UNQUOTED_FIELD_NAMES)
        .enable(StreamReadFeature.INCLUDE_SOURCE_IN_LOCATION)
        .build().registerKotlinModule()
    }
}
```

