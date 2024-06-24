---
title: .sh fájl futtathatóvá tétele giten
feed: show
date: 23-02-2024
---


## Probléma

Gitlab pipeline összeállításakor (de Jenkins is egy kutya) jött szembe, hogy ha a gradle-ös  projektet szeretnénk buildelni, akkor egy külön lépésben a `./gradlew`-nek egy tetszőleges chmod-dal, pl. `chmod u+x ./gradlew` minden futáskor futtatási jogot kell adni, mert a git by-default nem kezeli a jogosultságokat. Ezt simán ki lehet váltani a következővel:

```bash
git update-index --chmod=+x path/to/file
```

Így elvileg futtatható lesz, nem kell chmodolni. 😲 Arra érdemes figyelni, hogy ezt csak olyan fájlokra tudjuk futtatni, amik már verziókezelve lettek. Viszont, ha nem szeretnénk erre egy külön commit-ot elpocsékolni, akkor simán lehet squasholni egy másikkal. Én pl. a CI init kommitját és a gradle futtatást szeretem egybe squasholni.

> Forrás: [https://stackoverflow.com/questions/40978921/how-to-add-chmod-permissions-to-file-in-git](https://stackoverflow.com/questions/40978921/how-to-add-chmod-permissions-to-file-in-git)