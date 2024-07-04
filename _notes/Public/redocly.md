---
title: Redocly
feed: show
date: 04-07-2024
---

A Redocly segítségével a generált OpenAPI specifikációból tudunk egy letisztult, könnyen olvasható dokumentációt generálni.

### Előfeltétel

* Kell egy OpenAPI spec. Lsd.: [[OpenAPI Springdoc Setup]]
* Redocly [https://github.com/Redocly/redoc](https://github.com/Redocly/redoc) telepítése:
```bash
npm install -g redoc-cli
```

### Generálás

Ha csak fejlesztéskor kell újragenerálni a doksit, akkor a következő parancsot kell futtatni:

```bash
redocly build-docs http://localhost:8080/v3/api-docs --config ./config.yaml -o docs.html
```

Ahol: az URL a kliens által elérhető OpenAPI dokumentáció URL-je, amit a Swaggerből lehet lekérni. Swaggerben a cím alatt található egy link a következő szöveggel: /v3/api-docs. Ezt kell bemásolni a fenti parancsba.

> Ha már van eleve egy spec fájlunk, akkor annak az elérését kell megadni a fenti parancsban.

Ha már pl. ügyfélnek megy ki a doksi, akkor annyiban módosul az előbbi folyamat, hogy a kiválasztott openapi spec-et (json fájlt) le kell menteni, és azt kell megadni a redocly parancsban. 
A két generált HTML között annyi különbség lesz, hogy ha direkt letöltött fájlból generáljuk a doksit, akkor a Redocly letölthetővé teszi az openapi spec-et, a doksi elején egy Download gombbal. Fejlesztői doksiban nem fog működni a download link.

### Konfiguráció (opcionális)

A `--config` kapcsolóval megadható egy konfigurációs fájl, amiben a Redocly beállításait lehet módosítani. Például:

config.yaml:
```yaml
extends:
  - recommended


theme:
  openapi:
    theme: 
      logo: 
        gutter: 12px
    hideHostname: true
```

Bővebb infó: [https://redocly.com/docs/cli/configuration/](https://redocly.com/docs/cli/configuration/)