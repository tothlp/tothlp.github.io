---
title: PowerShell aliasok létrehozása
feed: show
slug: powershell-aliasok-letrehozasa
date: 30-07-2024
---

Először is, le kell kérni a default profil elérését, amire a legtutibb megoldás, ha futtatjuk a következőt:

```powershell
echo $PROFILE
```

Ennek eredményeképpen megkapjuk a default profil elérését, a továbbiakban ezt fogjuk használni.

Az adott fájlban tudjuk felvenni az aliasokat:

```powershell
function Go-Repo {Set-Location $env:USERPROFILE'\Developer'}
Set-Alias -Name repo -Value Go-Repo

function Ip-Address {curl https://checkip.amazonaws.com/}
Set-Alias -Name whatismyip -Value Ip-Address
```