---
title: JPA - Enum szűrőfeltétel
feed: show
date: 23-02-2024
---

```java
@Query("Select id from Data d where 'EU' = :#{#filter.customerType.name()}")
    List<DataDTO> findData(@Param("filter") DataRequest filter);
```

Kell a name(), egyébiránt Stringet hasonlítana Enum-hoz, és nem működne a lekérés.