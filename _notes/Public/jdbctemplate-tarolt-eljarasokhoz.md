---
title: JDBCTemplate tárolt eljárásokhoz
feed: show
date: 23-02-2024
---

## "Probléma"

A JDBC driver minden eljárás előtt meghív egy metadata lekérdezést, amivel lekéri, hogy az eljárások milyen nevű és típusú paramétereket várnak, mit adnak vissza, stb. Lehet olyan eset, amikor ez igencsak erőforrásigényes lehet.

lsd: [https://stackoverflow.com/questions/11934749/strange-sql-wasting-my-resources](https://stackoverflow.com/questions/11934749/strange-sql-wasting-my-resources)

## Megoldás

Legyen egy service amiből lehet új jdbccall-t lekérni, amin be van állítva a `withoutProcedureColumnMetaDataAccess`

```kotlin
@Component
class JdbcCallFactory(
    private val jdbcTemplate: JdbcTemplate
) {
    fun createJdbcCall(): SimpleJdbcCall = SimpleJdbcCall(jdbcTemplate).withoutProcedureColumnMetaDataAccess()
}
```

Ha ezt a jdbcCall-t használjuk fel, akkor nem lesz több metadata lekérés. Azonban azt figyelemben kell tartani, hogy ha így pl. rossz adattípust adunk meg, hiányzó paraméterünk van, stb. akkor nem az ORA-0xxx jellegű hibaüzenetek fognak visszajönni (ami esetleg a helyi Oracle mágus munkáját megkönnyítheti), hanem a java probál még értelmezhető üzenetet visszaadni, nyilván tök más leírással.