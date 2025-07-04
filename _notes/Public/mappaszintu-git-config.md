---
title: Mappaszintű Git konfiguráció
feed: show
date: 04-07-2025
slug: mappaszintu-git-config
---

> Forrás: [https://davenicoll.com/configuring-git-github-credentials-for-different-folders/](https://davenicoll.com/configuring-git-github-credentials-for-different-folders/)

## Use-case

Személy szerint ez nekem pl olyankor hasznos, ha különböző repokban különböző "userként" szeretnék dolgozni. Nyilván, ilyenkor nem jó commitonként állítgatni az authort.

## Megoldás

Úgy közelítettem meg, hogy az alapértelmezett értékek vannak felvéve a `~/.gitconfig` fájlban, és az ettől eltérő beállításokat kezelem külön.

pl:
```ini
[user]
    name = My Name
    email = user@email.com
```

Ezt követően  meg kell adni egy `includeIf` blokkot, ami akkor érvényesül, ha a reponk az adott mappán belül valahol található.

```ini
[includeIf "gitdir:~/Developer/Work/"]
    path = ~/.gitconfig-work-example
```

A `~/.gitconfig-work-example` fájlban pedig felvehetjük a céges beállításokat:

```ini
[user]
    name = My Work Name
    email = user@work.com
```
