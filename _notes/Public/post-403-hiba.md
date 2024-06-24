---
title: Spring - POST-nál 403-mas hiba
feed: show
date: 23-02-2024
---

## Probléma

Főleg új projektnél lehet belefutni, hogy a lekérő operációk (pl. GET) teljesen jól működnek, de a módosító operációk (mint POST) elhasalnak 403-mas hibával.

## Megoldás

Valószínűleg a CSRF tokennel van probléma. Vagy ezt kell megjavítani, vagy a SecurityConfig-ban ki lehet kapcsolni, de ezutóbbi nyilván nem ajánlott.