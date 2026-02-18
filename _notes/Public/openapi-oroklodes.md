---
title: OpenAPI Öröklődés
feed: show 
date: 18-02-2026
permalink: /openapi-oroklodes
---

OpenAPI spec "generáláshoz" hasznos lehet az [[OpenAPI Springdoc Setup]]. 

Ha viszont OpenAPI specből szeretnénk generálni, pl. szervert akkor belefuthatunk abba, hogy nem tudunk az objektumok között öröklődést létrehozni egy egyszerű allOf-fal. A `build.gradle`-ben a regisztrált tasknál meg kell adni a következőt is:

```kotlin
openapiNormalizer = [
        REF_AS_PARENT_IN_ALLOF: "true"    
]
```


