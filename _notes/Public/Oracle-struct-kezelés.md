---
title: Oracle struct kezelés
slug: oracle-struct-kezeles
feed: show
date: 12-08-2024
---

Amikor JdbcTemplate segítségével hívunk meg tárolt eljárásokat, meg kell adnunk a paraméterek nevét és típusát. A tömbök és struktúrák kezelése nem triviális.

Szükségünk lesz egy `DataSource`-ra, amiből lekérhető a `Connection`, erre több helyen is szükségünk lesz.

```kotlin
fun testStruct(dataLines: List<String>, user: Long): List<ContentResponse> {

    val schemaName = "MYSCHEMA"
    val catalogName = "PACKAGE"
    val prcedureName = "PROCEDURE_NAME"
    val arrayTypeName = "MYSCHEMA.PACKAGE.ARRAYTYPE"
    val structTypeName = "MYSCHEMA.PACKAGE.STRUCTTYPE"


    val jdbcCall = SimpleJdbcCall(jdbcTemplate)
        .withSchemaName(schemaName)
        .withCatalogName(catalogName)
        .withProcedureName(prcedureName)
        .declareParameters(
            SqlParameter("user", Types.NUMERIC),
            SqlParameter("myarray", Types.ARRAY, arrayTypeName),
            SqlOutParameter("out_array", Types.ARRAY, arrayTypeName)
        )

    // Handle IN array parameter

    val dataSource = jdbcTemplate.dataSource!!

    // Create array of structs. In this example, the struct has two fields: id and param2, first gets line, second is null
    val structArray = dataLines.map { line ->
        dataSource.connection.createStruct(structTypeName, arrayOf(line, null))
    }.toTypedArray()


    val conn: Connection = dataSource.connection
    val oraConn = conn.unwrap(OracleConnection::class.java)

    val params = MapSqlParameterSource()
        .addValue("user", user)
        .addValue("myarray", oraConn.createOracleArray(arrayTypeName, structArray))


    val result: Map<String, Any> = jdbcCall.withoutProcedureColumnMetaDataAccess().execute(params)

    // Handle OUT array parameter
    val resp = (result["out_array"] as java.sql.Array)
    val respArray = resp.array as Array<Any>

    var response =
    respArray.map {
        val attributes = (it as Struct).attributes
        val id = attributes[0] as String
        val param2 = (attributes[1] as? String)?

        ContentResponse(id, param2)
    }
    
    return response
}
```