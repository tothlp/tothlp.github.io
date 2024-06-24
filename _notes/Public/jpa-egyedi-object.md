---
title: JPA - Select egyedi objektumba
feed: show
date: 23-02-2024
---

Egyedi object létrehozás, ha pl a visszadandó értékhez kell tárolt eljárás is, stb. Megoldható native query-vel, és interface projection-nel.

```kotlin
interface InventoryResponse {
	var type: String?
	var inventoryValue: BigDecimal?
}

@Query("select t.tipus as type, USER.KESZLET(t.id) as inventoryValue from TIPUS_NEZ t",nativeQuery=true)
fun getInventory():List<InventoryResponse>
```

Ugyanez java-ban: (igen, kellenek a getek!!)

```java
public interface InventoryResponse{
	String getType();
	BigDecimal getInventoryValue();
}

@Query(value="select t.tipus as type, USER.KESZLET(t.id) as inventoryValue from TIPUS_NEZ t",nativeQuery=true)
List<InventoryResponse>getInventory();
```

Ha esetleg még enummal is kellene szűrni: [[JPA - Enum szűrőfeltétel]]