---
title: sudo git ctrl-z
feed: show
date: 23-02-2024
---

Ha valamit vissza akarunk vonni, squasholni, stb, hasznos lehet az interactive rebase. Én speciel olyankor használtam, amikor egy repo legelső commitjával kellett valamit utólag kezdeni.

```bash
git rebase -i HEAD~10 

git rebase -i --root
```
A root-tal a legelső commitra lehet ugrani. Fontos: ez egy erősen destruktív művelet, ha már pusholt commitokra akarjuk használni, akkor vszg. force pusholni is kell, szóval csak körültekintően! 

Azt viszont meg kell jegyezni, hogy nagyon szépen helyre lehet vele kozmetikázi a commit historyt.
